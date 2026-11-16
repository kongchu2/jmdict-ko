# JMdict-ko: a Japanese→Korean dictionary for Yomitan

> Sister dictionaries: [English → Korean (Wiktionary-en-ko)](https://github.com/kongchu2/wiktionary-en-ko) · [Chinese → Korean (CC-CEDICT-ko)](https://github.com/kongchu2/cedict-ko). Korean install guide: https://kongchu2.github.io/yomitan-ko/
> Found a wrong gloss? [Report a mistranslation](../../issues/new?template=mistranslation.yml).

[한국어](README.md) · English

A **Japanese→Korean** dictionary for [Yomitan](https://github.com/yomidevs/yomitan).
It keeps the entries, readings, parts of speech and sense divisions of [JMdict](https://www.edrdg.org/jmdict/j_jmdict.html) and translates each sense into Korean with an LLM.

| File | Contents |
|---|---|
| `jmdict-ko.zip` | Term dictionary. Korean glosses, part-of-speech and usage tags in Korean, the original English (collapsed), cross-reference links |
| `kanjidic-ko.zip` | Kanji dictionary. Korean 훈음 (meaning and reading, e.g. 배울 학), Sino-Korean reading, on/kun readings, stroke count and grade |

## Install

1. Download `jmdict-ko.zip` and `kanjidic-ko.zip` from [Releases](../../releases/latest).
2. In Yomitan, go to Settings → Dictionaries → Import and choose the zips.
3. New versions arrive through Yomitan's dictionary update check.

Inflected forms are found too (`食べた` → 食べる, `勉強した` → 勉強).

## Translation goal and progress

**Every JMdict entry (218,963) is translated.** Translation went in frequency order ([wordfreq](https://github.com/rspeer/wordfreq)), most common words first.

| Stage | Range | Model | Status |
|---|---|---|---|
| 1 | The 22,649 entries JMdict marks "common" | Claude Sonnet | ✅ Done |
| 2 | The next most frequent words (50,049 in total) | Claude Haiku | ✅ Done |
| 3 | All remaining entries (218,963 in total) | Claude Haiku | ✅ Done |
| — | 훈음 of 3,125 kanji (jōyō and jinmeiyō) | Claude | ✅ Done |

- As of 2026-11-03, 218,963 of 218,963 entries (100%) are translated. When JMdict changes the senses of an entry, that entry is left out of the build until it is translated again.
- About 5.5% (12,091 entries) are marked low confidence. The share is higher among rarer words.

## Translation quality

- The Korean glosses are machine-translated with an LLM (Claude) and can be wrong. Every entry keeps the original English (JMdict) in a collapsed section so you can check.
- Entries the model was unsure about carry the note `※ 자동 번역 신뢰도 낮음` (low-confidence machine translation).
- Reports and pull requests are welcome. To fix a gloss, edit `s` (the list of Korean glosses per sense) on the line with that JMdict id in `translations/terms.jsonl`.

## Building it yourself

You need Python 3.9 or later, and the [Claude Code](https://claude.com/claude-code) CLI (`claude`) to translate.

```sh
python3 -m venv .venv && .venv/bin/pip install -r requirements.txt

.venv/bin/python -m jako fetch                         # download JMdict/KANJIDIC2 and the Yomitan schemas
.venv/bin/python -m jako translate tags                # Korean labels for part-of-speech and usage tags
.venv/bin/python -m jako translate terms --limit 1000  # translate untranslated entries in frequency order (re-run to continue)
.venv/bin/python -m jako translate kanji               # 훈음 of jōyō and jinmeiyō kanji
.venv/bin/python -m jako build                         # dist/*.zip
.venv/bin/python -m jako validate                      # check against the Yomitan schemas and the deinflection rules
.venv/bin/python -m jako review -n 200                 # write a review sample to review/sample.md
```

- Translations accumulate in `translations/`, one line per JMdict id. An entry whose senses changed in JMdict is translated again on the next `translate` run and is left out of the build until then.
- `build --include-untranslated` also includes untranslated entries, with their English glosses.
- GitHub Actions (`.github/workflows/release.yml`) rebuilds from the latest JMdict every day and makes a release only when the content changed. Translation runs locally: commit `translations/` afterwards.

## License

- Code (`jako/`, `.github/`): [MIT](LICENSE).
- Dictionary data (`translations/`, the built zips): [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
  It is derived from JMdict and KANJIDIC2, © [Electronic Dictionary Research and Development Group](https://www.edrdg.org/edrdg/licence.html).
- Frequency data used to order the translation work: [wordfreq](https://github.com/rspeer/wordfreq) (CC BY-SA 4.0). It is not included in the built dictionaries.
