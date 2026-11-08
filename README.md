# TokenScope

TokenScope is a small browser lab where you change one condition and watch the arithmetic of a language model change with it.

It covers three mechanics: causal attention, token sampling, and byte-pair encoding (BPE). Every number on screen comes from synthetic examples that you can inspect and export as JSON. The lab runs entirely in your browser in English or Traditional Chinese, and it needs no account, no model API and no analytics.

[Open the lab](https://chih-kai-wang.github.io/tokenscope/?lang=en) · [繁體中文](README.zh-Hant.md) · [Mathematical conventions](docs/MATH.md)

![TokenScope attention workbench](docs/screenshots/attention.png)

## Three experiments

| Experiment    | Change                                                                   | Inspect                                                                                        |
| ------------- | ------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------- |
| **Attention** | Query vectors, selected position, causal mask, future-value perturbation | Signal paths, full weight matrix, raw scores, weighted output, exact perturbation delta        |
| **Sampling**  | Logits, temperature, top-k, top-p, uint32 seed                           | Before/after probabilities, entropy, candidate count, reproducible independent draws           |
| **BPE**       | English, Traditional Chinese, overlap example, or a custom corpus        | Frequency-weighted pair counts, individual merges, undo, symbol counts, complete merge history |

Each experiment ends with a short concept check. You can export the current inputs and computed results, and importing that JSON later restores the controls and recomputes every result. Switching language or tab keeps your experiment state until the page reloads. The URL records the language and the active tab, but not your inputs.

### Try this first

1. Open **Attention** and select the third query token.
2. Leave **Causal mask** on and enable **Perturb the last V**. The output stays unchanged.
3. Disable the mask. The future position now contributes, and the output changes.
4. Export the experiment to inspect the exact vectors and weights.

## Run locally

You need Node.js **22.12+**, and Node 24 also works.

```sh
git clone https://github.com/Chih-Kai-Wang/tokenscope.git
cd tokenscope
npm ci
npm run dev
```

Open the local URL that Vite prints. To serve a production build instead:

```sh
npm run build
npm run preview
```

Each release also ships a prebuilt static ZIP. Extract it and serve the directory with any static HTTP server, for example `python3 -m http.server 8080`. Opening `index.html` directly through `file://` does not work. The fonts are bundled, so the running lab calls no external API or font service. Only the first `npm ci` needs network access to npm.

## Verify

```sh
npm test                         # numerical, algorithm, and replay validation tests
npm run build                    # strict TypeScript + production bundle
npx playwright install chromium
npm run test:e2e                  # desktop + mobile Chromium
npm run format:check
```

The browser suite drives the real controls in both languages. It checks seeded replay, clearing of stale results, Unicode and empty corpora, downloads, language switching, keyboard tab navigation and horizontal overflow, and it runs automated axe accessibility checks. The mobile tests emulate an iPhone viewport in **Chromium**, not in Safari. Passing the automated accessibility checks does not establish full WCAG conformance.

GitHub Actions runs the same tests on every pull request and before each deployment of the static build to Pages. Pull requests are validated but never deployed.

## Scope and limitations

- **Attention** uses one self-attention head with five positions and hand-set two-dimensional Q, K and V vectors. It has no learned embeddings, positional encodings, training, residual connections or full Transformer blocks. The word labels are illustrative, and the weights are not a linguistic explanation.
- **Sampling** makes independent draws from fixed logits. The context is not updated between draws, so the displayed sequence is not autoregressive generation. The filtering order is documented explicitly because providers do not all use the same one.
- **BPE** works on Unicode code points within words, with no whitespace tokens and no end-of-word marker. It demonstrates the merge rule; it is not a production byte-level GPT tokenizer. The interface accepts 2,000 UTF-16 code units and up to 50 merges, while the reusable core allows 20,000 code units. Pair counts include overlaps, but replacement is non-overlapping.
- **State** lives only in page memory, so a refresh resets every experiment. Exports are plain JSON and contain the corpus or settings you entered. Importing a saved file replays it and recomputes all outputs; [replay limits and compatibility](docs/REPLAY.md) lists what the importer accepts.

## Project structure

```text
src/core/          Pure attention, sampling, and BPE functions + unit tests
src/components/    Three labs and shared accessible controls
src/App.tsx        Language and experiment navigation
src/style.css      Responsive workbench design and reduced-motion support
e2e/               Playwright interaction and accessibility checks
docs/              Conventions, design notes, and screenshots
```

The lab is built with React, TypeScript and Vite and needs no numerical runtime library. The pure functions in `src/core/` can be reused without the interface.

## References

- Vaswani et al. (2017), [Attention Is All You Need](https://arxiv.org/abs/1706.03762), introduced scaled dot-product attention and autoregressive masking.
- Holtzman et al. (2020), [The Curious Case of Neural Text Degeneration](https://arxiv.org/abs/1904.09751), introduced nucleus sampling.
- Sennrich, Haddow, and Birch (2016), [Neural Machine Translation of Rare Words with Subword Units](https://arxiv.org/abs/1508.07909), applied BPE to subword segmentation. This lab leaves out several of their preprocessing details, including end-of-word markers.

TokenScope is an independent educational project. It is not affiliated with Stanford or with any model provider.

## Contributing and license

Contribution guidelines are in [CONTRIBUTING.md](CONTRIBUTING.md). The code and the original educational text are released under the [MIT License](LICENSE). The bundled fonts keep their SIL Open Font Licenses, as described in the [third-party notices](docs/THIRD_PARTY.md).
