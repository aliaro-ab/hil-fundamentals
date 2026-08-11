# HIL Fundamentals

A vendor-neutral reference vault on the concepts behind hardware-in-the-loop (HIL) test systems: no product names, no APIs, no code. It is for engineers who are competent in embedded software, controls, or test automation, but new to HIL specifically. When you eventually read a real standard or a real product manual, this reference helps you navigate vendor and industry terminology.

The core idea the vault builds up: a test bench is a machine that can **observe, stimulate, capture, replay, disturb, and interrogate** a device across six different boundaries. The discipline is about choosing the right verb at the right boundary, then exercising that on a real-time controller.

## Where to start

Read [start-here.md](start-here.md). It contains the full reading order (plan for about half a day), followed by practice scenario cards and next steps such as the bench evaluation checklist and glossary.

If your background is automated software testing, skim [coming-from-software-testing.md](coming-from-software-testing.md) first.

## How to use this vault with Tolaria

This repository is a [Tolaria](https://github.com/refactoringhq/tolaria) vault. Tolaria is a local-first Markdown knowledge base: notes are plain Markdown files with YAML frontmatter, connected by `[[wikilinks]]` and organized by note types rather than folders.

1. Install Tolaria.
2. Clone this repository:

   ```sh
   git clone https://github.com/aliaro-ab/hil-fundamentals
   ```

3. In Tolaria, open the cloned folder as a vault.

Tolaria picks up the note types automatically and provides a built-in view per type, so you can browse by Guide, Concept, and so on. Agent-facing conventions for the vault live in [AGENTS.md](AGENTS.md).

### Without Tolaria

Everything is plain Markdown, so the vault is readable in any editor. Wikilinks like `[[the-capability-grid]]` refer to the file of the same name (`the-capability-grid.md`). Editors with wikilink support (Obsidian and similar) will resolve them as clickable links.

## Vault organization

Each note has a `type:` in its frontmatter. Organization comes from types and wikilink relationships, not folder structure — all notes live in the vault root.

| Type | What it is for |
| --- | --- |
| Guide | Read start to finish |
| Concept | Load-bearing ideas |
| Boundary | Surfaces of the device you can touch |
| Capability | The six verbs |
| Pitfall | Mistakes you get to make only once |
| Scenario | Practice cards |

Types themselves are notes (files like `guide.md` with `type: Type`), which is how Tolaria defines their icons, colors, and suggested properties.

## License

[MIT](LICENSE)
