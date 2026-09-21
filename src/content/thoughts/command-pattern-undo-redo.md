---
title: "Undo and Redo Without Rewriting Your App: The Command Pattern in TypeScript, Qt, and C++"
description: "How to retrofit undo and redo into an existing app with the Command pattern, with concrete examples in TypeScript, Qt (C++), and raw C++, plus alternatives and what the pattern actually buys you."
pubDate: 2026-09-21
tags: ["command-pattern", "undo-redo", "typescript", "qt", "cpp", "software-design", "design-patterns"]
draft: false
---

Most apps grow without undo. Then someone hits Ctrl+Z and nothing happens. Retrofitting undo feels scary because every action already mutates state directly. The Command pattern is the usual way out: wrap each change as an object that knows how to do it and how to undo it, then keep a history of those objects.

This post covers what the pattern is, how to drop it into an existing codebase, concrete shapes in TypeScript, Qt (C++), and raw C++, what else you might use instead, and what you actually gain (and pay for).

## What problem are we solving?

Without a history model, "undo" means reverse-engineering every mutation path. That does not scale. With commands, each user action becomes:

1. Build a command that captures enough data to apply and reverse the change.
2. Execute it once.
3. Push it onto an undo stack.
4. On undo, pop, call `undo()`, push onto a redo stack.
5. On redo, pop from redo, call `execute()` again, push back onto undo.

New actions clear the redo stack. That is the whole mental model.

## The pattern in one interface

```ts
interface Command {
  execute(): void;
  undo(): void;
  // optional: redo() if execute is not idempotent / not safe to replay
}
```

Everything else — stacks, coalescing keystrokes, macros, transactional batches — is built on top of that.

## How to introduce it into an app that never had it

You do not need to rewrite the world on day one.

1. **Pick one high-value surface** (document edits, canvas moves, form fields). Leave the rest as-is.
2. **Stop calling mutators from the UI.** UI handlers should create a command and hand it to an invoker (`History` / `UndoStack`), not touch the model directly.
3. **Put the "how" inside the command**, or behind a model API the command calls. Prefer small, intentional model methods (`setTitle`, `moveItem`) over dumping all logic into the command.
4. **Capture before-state at construction time** (or on first execute) so undo does not depend on "current" values that may have changed.
5. **Grow coverage command by command.** Each new feature ships with a command; old paths migrate when you touch them.

A practical rule: if two UI entry points can do the same change (toolbar button and shortcut), both must go through the same command. Otherwise undo history and product behavior diverge.

## TypeScript

A tiny history manager plus a concrete edit:

```ts
interface Command {
  execute(): void;
  undo(): void;
}

class History {
  private undoStack: Command[] = [];
  private redoStack: Command[] = [];

  run(cmd: Command): void {
    cmd.execute();
    this.undoStack.push(cmd);
    this.redoStack.length = 0;
  }

  undo(): void {
    const cmd = this.undoStack.pop();
    if (!cmd) return;
    cmd.undo();
    this.redoStack.push(cmd);
  }

  redo(): void {
    const cmd = this.redoStack.pop();
    if (!cmd) return;
    cmd.execute();
    this.undoStack.push(cmd);
  }
}

type Doc = { title: string };

class SetTitleCommand implements Command {
  private previous!: string;

  constructor(
    private readonly doc: Doc,
    private readonly nextTitle: string,
  ) {}

  execute(): void {
    this.previous = this.doc.title;
    this.doc.title = this.nextTitle;
  }

  undo(): void {
    this.doc.title = this.previous;
  }
}

// UI
const history = new History();
const doc: Doc = { title: "Untitled" };

history.run(new SetTitleCommand(doc, "Quarterly plan"));
history.undo(); // back to "Untitled"
history.redo(); // "Quarterly plan" again
```

For typing-heavy UIs, coalesce: while the user is typing in one field, update the *same* command's `nextTitle` (or merge into the last stack entry) instead of pushing one command per keystroke.

Composite commands (multi-select delete, paste) are just a command that holds a list and runs `execute` / `undo` in the right order (undo in reverse).

## Qt (C++)

Qt already has `QUndoCommand` and `QUndoStack`. Prefer them over inventing a parallel stack.

```cpp
#include <QUndoCommand>
#include <QUndoStack>
#include <QString>

class Document {
public:
    QString title() const { return m_title; }
    void setTitle(const QString& t) { m_title = t; }
private:
    QString m_title { "Untitled" };
};

class SetTitleCommand : public QUndoCommand {
public:
    SetTitleCommand(Document* doc, QString next, QUndoCommand* parent = nullptr)
        : QUndoCommand(parent)
        , m_doc(doc)
        , m_next(std::move(next))
        , m_prev(doc->title())
    {
        setText(QObject::tr("Set title"));
    }

    void redo() override { m_doc->setTitle(m_next); }
    void undo() override { m_doc->setTitle(m_prev); }

private:
    Document* m_doc;
    QString m_next;
    QString m_prev;
};

// somewhere in the app
QUndoStack stack;
Document doc;
stack.push(new SetTitleCommand(&doc, "Quarterly plan"));
stack.undo();
stack.redo();
```

Notes that matter in real Qt apps:

- `QUndoStack::push` calls `redo()` on the command. Implement the forward path in `redo()`, not only in a custom `execute`.
- Use `QUndoCommand` children (or `beginMacro` / `endMacro`) for multi-step edits that should undo as one.
- Wire `QAction`s with `createUndoAction` / `createRedoAction` so menus and shortcuts stay consistent.
- For mergeable edits (slider scrubbing, continuous typing), override `id()` and `mergeWith()`.

## Raw C++

Same idea without Qt's helpers: own the stack and use `unique_ptr` (or a custom allocator) so ownership is clear.

```cpp
#include <memory>
#include <string>
#include <vector>

struct Command {
    virtual ~Command() = default;
    virtual void execute() = 0;
    virtual void undo() = 0;
};

class History {
public:
    void run(std::unique_ptr<Command> cmd) {
        cmd->execute();
        undo_.push_back(std::move(cmd));
        redo_.clear();
    }

    void undo() {
        if (undo_.empty()) return;
        auto cmd = std::move(undo_.back());
        undo_.pop_back();
        cmd->undo();
        redo_.push_back(std::move(cmd));
    }

    void redo() {
        if (redo_.empty()) return;
        auto cmd = std::move(redo_.back());
        redo_.pop_back();
        cmd->execute();
        undo_.push_back(std::move(cmd));
    }

private:
    std::vector<std::unique_ptr<Command>> undo_;
    std::vector<std::unique_ptr<Command>> redo_;
};

struct Document {
    std::string title { "Untitled" };
};

class SetTitleCommand : public Command {
public:
    SetTitleCommand(Document& doc, std::string next)
        : doc_(doc), next_(std::move(next)) {}

    void execute() override {
        prev_ = doc_.title;
        doc_.title = next_;
    }

    void undo() override {
        doc_.title = prev_;
    }

private:
    Document& doc_;
    std::string next_;
    std::string prev_;
};
```

In larger C++ codebases, keep commands free of UI types. Pass model references or IDs, not widgets. That keeps headless tests and scripting possible.

## Alternatives (and when they win)

| Approach | Idea | Good when | Pain |
| --- | --- | --- | --- |
| **Command** | Explicit do/undo per action | Interactive editors, desktop apps | Many small classes; easy to forget a path |
| **Memento** | Snapshot whole state (or a slice) before change | Small documents; opaque state | Memory; coarse undo; hard with large graphs |
| **Event sourcing** | Append domain events; rebuild state | Audit, sync, time-travel as a product feature | Bigger architecture; migration story |
| **Operational transform / CRDT** | Transform concurrent ops | Collaborative editing | Specialized; usually overkill for single-user undo |
| **Transaction log + inverse ops** | DB-style log of inverse statements | Persistence-heavy systems | Closer to storage than UX |
| **Diff / patch stacks** | Store reverse diffs of a document | Text and structured docs | Need a solid diff model |

Hybrid is common: commands for user intent, mementos inside a few "too hard to invert" operations (e.g. replace entire subgraph), or event sourcing under the hood with a command façade for the UI.

## What the pattern brings into the app

**Upside**

- Real undo/redo without ad-hoc reverse code scattered in controllers.
- One place to add logging, analytics, soft permissions, or macros ("replay last N").
- Clearer separation: UI asks for an intent; the model change is explicit and testable.
- Batching and transactional UX (select → delete → one undo) become natural.

**Cost**

- More types and ceremony for simple apps.
- Every new mutation path must be command-aware or you get "ghost" changes that never undo.
- Lifetime and ownership bugs (especially in C++) if commands hold dangling pointers.
- Naive per-keystroke commands explode the stack unless you merge.

**Design pressure (this is usually healthy)**

- You start naming user intents (`RenameLayer`, `NudgeSelection`) instead of "whatever the button click did."
- Side effects (network, disk) force a policy: undo local only, or compensate remotely? Decide early.

## A minimal retrofit checklist

1. Introduce `History` / `QUndoStack` and route one feature through it.
2. Ban direct model writes from that feature's UI layer (lint or code review).
3. Add undo/redo actions and shortcuts once.
4. Add merging for continuous input where needed.
5. Expand feature by feature; do not wait for a "big bang" migration.

## Closing

Undo is not a feature you bolt onto random setters. It is a history of reversible intents. The Command pattern is the smallest widely understood way to express that in TypeScript, Qt, and plain C++. Start with one surface, keep commands focused on model changes, and let the stack own time. Alternatives exist when your problem is collaboration, full audit trails, or tiny documents that are cheaper to snapshot — but for classic single-user undo/redo, commands remain the default for a reason.
