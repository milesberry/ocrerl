# Colophon

## How This Project Was Built

This OCR Exam Reference Language editor was developed collaboratively between a human developer and Claude (Anthropic's AI assistant), working together through Claude Code -- Anthropic's command-line tool for AI-assisted software development.

## Development Process

The project was built in a single extended session, evolving through rapid iteration and conversation. The human developer provided the requirements, design direction, and quality assurance; Claude wrote the code.

### Starting Point

The project began with a broken prototype -- a Blockly-based editor that had stopped working due to an API change in Blockly v12. The initial task was simply to fix the error message: *"JavaScript generator does not know how to generate code for block type 'print_statement'"*. This turned out to be a namespace change in Blockly's generator API, where `Blockly.JavaScript` had been replaced by a separate module.

### Feature Development

Once the editor was functional, the OCR GCSE Computer Science specification PDF was provided as a reference. Claude compared the specification against the implementation and identified eleven missing language features. These were implemented in a batch: casting, integer division, exponentiation, switch/case, string operations (substring, left, right, ASC, CHR), file handling with a virtual file system, 2D arrays, array initialisation with values, and comments.

### Iterative Refinement

The bulk of the development time was spent on iterative refinement, driven by the human developer testing the editor and feeding back issues. Key iterations included:

- **Moving from modal to inline input** -- user input was originally handled via browser `prompt()` dialogs; this was replaced with a terminal-style inline input within the output panel.

- **Adding an ERL code display** -- the generated code panel initially showed the JavaScript being executed. A second Blockly code generator was created to output OCR ERL pseudocode instead. This required implementing the generator lifecycle methods (`init`, `finish`, `scrub_`) that Blockly's custom generators need.

- **Complete syntax overhaul** -- the human developer provided detailed feedback on every block's visual appearance and generated code, comparing each against the OCR specification. Labels were corrected (removing Blockly's default "set" and "do" labels), structural elements were added (`next i`, `endwhile`, `endif`, `endswitch`, `endprocedure`, `endfunction`), and the entire toolbox was reorganised into categories matching the specification.

- **Block variant strategy** -- a pattern emerged of providing both "literal" and "expression" versions of blocks (input, print, procedure/function definitions and calls). The literal versions are simpler for beginners; the expression versions accept plugged-in blocks for more advanced use.

- **Bug fixing through use** -- several issues were caught through testing, including: `const` declarations causing redeclaration errors on re-run (fixed by generating `var` instead), procedure parameter fields creating spurious variables on every keystroke (fixed by debouncing variable creation), and Blockly's built-in operator blocks showing wrong symbols (`x` and `÷` instead of `*` and `/`).

### Save/Load and File System

Program persistence was added through JSON download/upload using Blockly's built-in serialisation. An initial localStorage-based save/load system was built but then simplified down to just download and upload, keeping the interface clean.

The virtual file system was extended to allow loading real text files into the VFS for programs to read, and downloading files that programs had written -- bridging the gap between the simulated file handling and actual files on the user's machine.

## Technical Architecture

The entire application is a single HTML file containing all CSS, HTML, and JavaScript. This was a deliberate choice for portability -- teachers can distribute it as a single file, host it on any web server, or open it directly from a file system.

The application uses two Blockly code generators running in parallel:
- A **JavaScript generator** that produces executable code, run via `eval()` inside an async wrapper to support the Promise-based input system
- An **ERL generator** that produces OCR pseudocode for the display panel, updating live as blocks are arranged

## Tools Used

- **Claude Code** (Anthropic) -- AI-assisted development environment
- **Claude** (Anthropic, Claude Opus 4) -- AI pair programmer that wrote the code
- **Google Blockly** -- visual block programming library
- **A web browser** -- for testing

## Acknowledgements

The OCR Exam Reference Language is defined in the [OCR GCSE Computer Science (J277) specification](https://www.ocr.org.uk/Images/558027-specification-gcse-computer-science-j277.pdf).

This project demonstrates a model of human-AI collaboration in software development: the human provides domain expertise, design judgement, and quality feedback; the AI handles code generation, API knowledge, and rapid iteration. Neither could have built this as effectively alone.
