# McCabe's Razor

> Measure complexity. Don't game it.

AI is good at making code work. It is less good at noticing when the fifth edge
case turns a clear path into a maze. The code still passes; it just gets
harder to review, test, and safely change.

McCabe's Razor keeps an eye on that. It helps coding agents shape
decision-heavy code before writing it, then clean up existing hotspots without
shuffling the same branches into tiny helpers. Cyclomatic and cognitive
complexity are clues, not targets. The goal is boring: simpler control flow,
same behavior.

## Use

```text
Use McCabe's Razor while implementing this.
```

```text
Review this module with McCabe's Razor.
```

## Install

### Codex

> Use `$skill-installer` to install
> `https://github.com/batuhanbadak/mccabes-razor/tree/main/skills/mccabes-razor`.

### Claude Code

```text
/plugin marketplace add batuhanbadak/mccabes-razor
/plugin install mccabes-razor@mccabes-razor
```

![A GitHub-style diff simplifying a realtime event reducer](./assets/realtime-event-refactor.png)

![A GitHub-style diff replacing nested inventory scans with indexed lookup](./assets/inventory-allocation-refactor.png)

[MIT](./LICENSE)
