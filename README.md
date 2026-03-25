# wordforge

Modern password wordlists and rules to supplement `rockyou.txt`.

## The Problem

`rockyou.txt` was leaked in 2009 from the RockYou breach. Seventeen years later, it's still the default wordlist for password cracking — but the passwords people use have changed. Cultural references, multilingual phrases, leet-speak patterns, and modern slang are poorly represented in a list frozen in 2009.

**wordforge** provides research-driven supplements that capture what `rockyou.txt` misses.

## Quick Start

```bash
# Clone the repo
git clone https://github.com/doritoes/wordforge.git

# Drop-in replacement for rockyou.txt + OneRule
hashcat -m <hash_type> hashes.txt wordforge/nocap.txt -r wordforge/nocap.rule

# Or use the thicc profile for maximum coverage (+0.25pp over rockyou)
hashcat -m <hash_type> hashes.txt wordforge/thicc.txt -r wordforge/thicc.rule
```

## Files at a Glance

| File | Entries | Size | Compressed | Description |
|------|---------|------|------------|-------------|
| `nocap.txt` | 14.3M | 134 MB | 51 MB (.gz) | Drop-in `rockyou.txt` replacement |
| `nocap.rule` | 48,452 | 477 KB | — | Drop-in `OneRuleToRuleThemStill` replacement |
| `thicc.txt` | 14.4M | 134 MB | 48 MB (.gz) | nocap.txt + multilingual cohorts |
| `thicc.rule` | 48,715 | 477 KB | — | nocap.rule + unobtainium.rule |
| `nocap-plus.txt` | 14.4M | 148 MB | 86 MB (.gz) | Bleeding-edge extended wordlist |
| `rizzyou.txt` | 223 | 2 KB | — | GenZ supplement (one word per line) |
| `bussin.rule` | 38 | <1 KB | — | Supplement rules not in OneRule |
| `unobtainium.rule` | 263 | 3.2 KB | — | Surgical high-value rules |

All files are UTF-8, one entry per line, compatible with hashcat and John the Ripper.

## Wordlists

### rizzyou.txt

GenZ-era password roots that `rockyou.txt` cannot contain — the RockYou breach was in 2009, before Minecraft, Fortnite, BTS, TikTok, and Bitcoin existed. 223 words, clean wordlist (one word per line, no comments). Every term has zero matches in rockyou.txt and 1,000+ appearances in HIBP Pwned Passwords.

Categories: gaming, music, K-pop, streamers/YouTubers, anime, movies/TV, memes, Gen Z slang, social platforms, apps, crypto, AI, COVID era, sports, streetwear.

```bash
# Use rizzyou.txt standalone with rules
hashcat -m <hash_type> hashes.txt rizzyou.txt -r OneRuleToRuleThemStill.rule

# Or combine with rockyou.txt to create nocap.txt
cat rockyou.txt rizzyou.txt > nocap.txt
```

### nocap.txt

The combined baseline wordlist:

```
rockyou.txt + rizzyou.txt = nocap.txt
```

Drop-in replacement for `rockyou.txt` in any cracking workflow. Everything rockyou has, plus modern coverage.

### nocap-plus.txt

Extended wordlist with additional cohort files:

```
nocap.txt + cohort wordlists = nocap-plus.txt
```

Cohort wordlists target specific linguistic and cultural communities with dedicated vocabulary. Use `nocap-plus.txt` when you want maximum coverage.

#### Cohort Wordlists

| Cohort | Description |
|--------|-------------|
| Turkish | Common Turkish password roots |
| Indian | Hindi/Indian language password patterns |
| Arabic | Arabic-origin password roots |
| Slavic | Slavic language password patterns |
| Chinese Pinyin | Romanized Chinese password roots |
| Korean | Romanized Korean password patterns |
| Portuguese-Brazilian | Portuguese/Brazilian password roots |
| Spanish phrases | Spanish compound phrases and word combinations |
| French phrases | French compound phrases and word combinations |
| Culture/Sports/Music | Media titles, athlete names, band references |
| English leet phrases | Number-substitution patterns (2=to, 4=for) |
| Markov phrase roots | Statistically discovered word sequences and compounds |

## Rules

### nocap.rule

Drop-in replacement for [OneRuleToRuleThemAll](https://github.com/NotSoSecure/password_cracking_rules) / [OneRuleToRuleThemStill](https://github.com/stealthsploit/OneRuleToRuleThemStill).

```
OneRuleToRuleThemStill.rule + bussin.rule = nocap.rule
```

Built on the OneRule foundation — the community's most widely used hashcat rule set. `bussin.rule` adds rules genuinely missing from OneRule, discovered through analysis of 11.5M+ cracked HIBP passwords. Deduplicated with space-normalized comparison (hashcat treats `$2$0$1$5` and `$2 $0 $1 $5` identically). 48,452 unique rules, zero duplicates.

### bussin.rule

The supplement that makes nocap.rule more than just OneRule. Contains **only** rules verified as missing from OneRuleToRuleThemStill (38 rules):

- **Single-digit appends**: `$1 $2 $3 $5 $7` — OneRule has `$0 $4 $6 $8 $9` but omits these five
- **Single-digit prepends**: `^0` through `^9` — highest-hit rules discovered (1,052-1,725 diamond hits each)
- **Double-digit appends**: `$0 $0` through `$9 $9` — OneRule only has none of these
- **Short year**: `$2 $2` — the only 2-digit year suffix not in OneRule
- **Triple-digit repeats**: `$1 $1 $1` through `$9 $9 $9` — OneRule only has `$4 $4 $4`
- **Core leet substitution**: `sa@ se3 si1 so0` — fundamental a→@, e→3, i→1, o→0 transform
- **Keyboard walk suffixes**: `$q $w $e`, `$a $s $d`, `$z $x $c`, `$q $a $z` — universal keyboard patterns

Every rule was confirmed missing through normalized comparison against OneRule's 48,414 rules and validated against real crack data.

```bash
# Instead of:
hashcat -m 0 hashes.txt rockyou.txt -r OneRuleToRuleThemStill.rule

# Use:
hashcat -m 0 hashes.txt nocap.txt -r nocap.rule
```

### unobtainium.rule

Surgical, high-value rule set — the opposite philosophy from nocap.rule. Where nocap.rule is comprehensive (48.5K rules), unobtainium.rule is minimal (263 rules) with every rule earning its place through measured crack contribution against HIBP data. No filler.

Derived by analyzing 25.9 million cracked passwords, identifying transformation patterns that produce disproportionate results, and diffing against nocap.rule to capture what the broad set misses. Includes digit prepends/appends, year suffixes (including 5-char year+special patterns like `$2$0$2$4$!`), capitalize+suffix combos, keyboard walk suffixes, leet substitutions, and special character patterns.

Best paired with the larger wordlists for a fast, high-yield pass:

```bash
hashcat -m <hash_type> hashes.txt nocap-plus.txt -r unobtainium.rule
```

## Effectiveness

Tested against HIBP Pwned Passwords (SHA-1). All three profiles benchmarked on the same 10-batch gravel sample (4,967,165 hashes, evenly spaced across 4,328 batches):

| Attack | Cracked | Rate | Time (RTX 4060 Ti) |
|--------|---------|------|--------------------|
| `rockyou.txt` + `OneRuleToRuleThemStill.rule` | 1,488,273 | **29.96%** | ~171s |
| `nocap.txt` + `nocap.rule` | 1,490,372 | **30.00%** | 171.5s |
| `thicc.txt` + `thicc.rule` | 1,500,730 | **30.21%** | 171.2s |

### Analysis

**nocap vs rockyou+OneRule (+0.04pp):** Effectively identical. nocap.txt is a safe drop-in replacement — same performance, same run time, with modern cultural coverage baked in.

**thicc vs rockyou+OneRule (+0.25pp):** The cohort wordlists and unobtainium rules produce a measurable improvement. 0.25 percentage points sounds small, but projected across the full 2.15 billion HIBP hash corpus, that delta represents approximately **5.4 million additional cracked passwords** — real passwords that rockyou+OneRule misses entirely. Run time is identical.

**thicc vs nocap (+0.21pp):** The improvement comes from two sources: 12 multilingual cohort wordlists (Turkish, Arabic, Slavic, Chinese Pinyin, Korean, Portuguese, Indian, Spanish, French, English leet phrases, Markov-discovered roots) and 263 surgical rules derived from analyzing 25.9 million cracked HIBP passwords. Projected across HIBP: ~4.5 million additional cracks.

The 30% rate is remarkably stable batch-to-batch (29.66%–30.33% range across all 4,328 batches), confirming that HIBP batches are statistically equivalent samples of the full password space.

## Performance

Keyspace and estimated run times for common attack pairings:

| Wordlist | Rule | Keyspace | RTX 4060 Ti | RTX 4090 |
|----------|------|----------|-------------|----------|
| `nocap.txt` | `nocap.rule` | 695B | ~171s | ~20s |
| `thicc.txt` | `thicc.rule` | 703B | ~171s | ~20s |
| `nocap-plus.txt` | `unobtainium.rule` | 3.8B | ~0.5s | ~0.1s |

*Times are for SHA-1 (mode 100) with `-O` optimized kernels. MD5/NTLM will be faster, bcrypt/scrypt dramatically slower.*

The keyspace for `nocap.txt + nocap.rule` is comparable to `rockyou.txt + OneRuleToRuleThemStill` — no significant time penalty for the additional coverage.

## Methodology

All wordlists and rules were tested against HIBP Pwned Passwords (SHA-1) using a structured pipeline:

1. **Discovery** — Generate candidate roots via Markov chains, corpus mining, and cultural research
2. **Validation** — Every root verified against HIBP k-anonymity API (minimum breach frequency threshold)
3. **Testing** — Full hashcat runs on sampled HIBP batches measuring per-attack crack rates
4. **Feedback** — Cracked passwords analyzed to discover new roots and rule patterns, feeding the next iteration

### Attack Pairing Principle

Pair incremental files with established counterparts for maximum effectiveness:

| Wordlist | Rule | Purpose |
|----------|------|---------|
| `thicc.txt` | `thicc.rule` | Maximum coverage (recommended) |
| `nocap-plus.txt` | `unobtainium.rule` | High-value surgical cracks |
| `nocap-plus.txt` | `nocap.rule` | Broad coverage |

## Usage

### Basic — Replace rockyou.txt

```bash
# Instead of:
hashcat -m 0 hashes.txt rockyou.txt

# Use:
hashcat -m 0 hashes.txt nocap.txt
```

### With Rules

```bash
# Broad attack
hashcat -m 0 hashes.txt nocap-plus.txt -r nocap.rule

# Surgical attack
hashcat -m 0 hashes.txt nocap-plus.txt -r unobtainium.rule
```

### Both Rules Combined

```bash
# Run surgical first (fast), then broad
hashcat -m 0 hashes.txt nocap-plus.txt -r unobtainium.rule
hashcat -m 0 hashes.txt nocap-plus.txt -r nocap.rule
```

## Complementary Attacks

Dictionary+rules attacks find passwords built from words. They do **not** replace brute-force and mask attacks, which cover passwords that no wordlist contains — random strings, phone numbers, PINs, and short passwords.

A complete cracking workflow runs both. In our production pipeline against HIBP Pwned Passwords, dictionary+rules account for roughly 40% of total cracks. The other 60% come from brute-force and mask attacks.

### Recommended non-rule attacks

Run these alongside your dictionary+rules passes. Times are for SHA-1 on an RTX 4060 Ti with `-O`.

**Brute-force (exhaustive — all printable characters):**

```bash
# Covers ALL passwords 1-7 characters. Non-negotiable.
hashcat -m <hash_type> hashes.txt -a 3 ?a              # 1 char — instant
hashcat -m <hash_type> hashes.txt -a 3 ?a?a             # 2 chars — instant
hashcat -m <hash_type> hashes.txt -a 3 ?a?a?a            # 3 chars — instant
hashcat -m <hash_type> hashes.txt -a 3 ?a?a?a?a           # 4 chars — instant
hashcat -m <hash_type> hashes.txt -a 3 ?a?a?a?a?a          # 5 chars — ~1 sec
hashcat -m <hash_type> hashes.txt -a 3 ?a?a?a?a?a?a         # 6 chars — ~1.7 min
hashcat -m <hash_type> hashes.txt -a 3 ?a?a?a?a?a?a?a        # 7 chars — ~107 min
```

**Pure digit masks (phone numbers, PINs):**

```bash
# Essentially free — <2 minutes total for all four
hashcat -m <hash_type> hashes.txt -a 3 ?d?d?d?d?d?d?d?d?d           # 9 digits — <1 sec
hashcat -m <hash_type> hashes.txt -a 3 ?d?d?d?d?d?d?d?d?d?d          # 10 digits — ~1 sec
hashcat -m <hash_type> hashes.txt -a 3 ?d?d?d?d?d?d?d?d?d?d?d         # 11 digits — ~9 sec
hashcat -m <hash_type> hashes.txt -a 3 ?d?d?d?d?d?d?d?d?d?d?d?d        # 12 digits — ~92 sec
```

**8-char character class masks (the escalation ladder):**

```bash
# Each mask is a strict subset of the next — run cheapest first
hashcat -m <hash_type> hashes.txt -a 3 ?l?l?l?l?l?l?l?l                    # lowercase 8 — ~19 sec
hashcat -m <hash_type> hashes.txt -a 3 -1 ?l?d ?1?1?1?1?1?1?1?1            # lower+digit 8 — ~4.3 min
```

**9-10 char structured masks:**

```bash
hashcat -m <hash_type> hashes.txt -a 3 ?l?l?l?l?l?l?l?l?l                  # lowercase 9 — ~10 min
hashcat -m <hash_type> hashes.txt -a 3 ?u?l?l?l?l?l?l?l?d                  # Cap+7lower+1digit — ~8 sec
hashcat -m <hash_type> hashes.txt -a 3 ?u?l?l?l?l?l?d?d                    # Cap+5lower+2digit — ~5 sec
hashcat -m <hash_type> hashes.txt -a 3 ?u?l?l?l?l?l?l?l?d?d                # Cap+7lower+2digit — ~32 min
```

**Hybrid attacks — word + suffix (`-a 6`):**

```bash
# Digit suffixes — catches "password1234", "monkey20241"
hashcat -m <hash_type> hashes.txt -a 6 nocap-plus.txt ?d?d?d?d             # word + 4 digits — ~6 sec
hashcat -m <hash_type> hashes.txt -a 6 nocap-plus.txt ?d?d?d?d?d           # word + 5 digits — ~3 min
hashcat -m <hash_type> hashes.txt -a 6 nocap-plus.txt ?a?a?a               # word + 3 any chars — ~25 min

# Digit+special suffixes — catches "password123!", "monkey2024@"
# Analysis of 18.5M complex passwords shows 47% have digit+special suffix structure
hashcat -m <hash_type> hashes.txt -a 6 nocap-plus.txt ?d?d?d?s             # word + 3 digits + 1 special — ~2 min
hashcat -m <hash_type> hashes.txt -a 6 nocap-plus.txt ?d?d?d?d?s           # word + 4 digits + 1 special — ~19 min

# Special+digit prefix — catches "password!123"
hashcat -m <hash_type> hashes.txt -a 6 nocap-plus.txt ?s?d?d?d             # word + 1 special + 3 digits — ~2 min
```

**Note on hybrid speed:** Hybrid attacks (`-a 6` / `-a 7`) with large dictionaries like `nocap-plus.txt` (148 MB) run at roughly **0.37x mask speed** due to dictionary I/O. On an RTX 4060 Ti, expect ~4 GH/s for SHA-1 hybrids vs ~10.9 GH/s for pure masks. Smaller dictionaries (under 10 MB) run at ~0.74x mask speed.

### Suggested run order

1. Brute-force 1-6 chars (instant)
2. Digit masks 9-12 (instant)
3. 8-char lowercase + lowercase+digit masks (~5 min)
4. Dictionary + unobtainium.rule (<1 sec)
5. Dictionary + nocap.rule (~1.5 min)
6. Hybrid word + 4 digits + word + 3d1s (~8 sec + ~2 min)
7. Brute-force 7 chars (~107 min)
8. 9-10 char structured masks (~50 min)
9. Extended hybrids: word + 5 digits, word + 3 any, word + 4d1s (~47 min)

Total: roughly 3.5 hours for a thorough pass on SHA-1.

## Responsible Use

These wordlists and rules are provided for **authorized security testing only**:

- Penetration testing with written authorization
- CTF competitions and security challenges
- Security research and password policy evaluation
- Auditing your own accounts and systems

Do not use these tools for unauthorized access to systems or accounts you do not own or have explicit permission to test. Unauthorized access to computer systems is illegal in most jurisdictions.

## Version

**Current release:** v2.0 (March 2026)

Wordlists and rules are actively maintained. New roots and patterns are added as they are discovered and validated through the feedback pipeline.

| Date | Change |
|------|--------|
| 2026-03-25 | **v2.0:** Added thicc.txt + thicc.rule attack profile. Three-way benchmark on HIBP gravel: rockyou+OneRule 29.96%, nocap 30.00%, thicc 30.21% (+0.25pp = ~5.4M extra cracks across HIBP). unobtainium.rule finalized at 263 rules (removed 1 duplicate with nocap.rule, cleaned internal comments). Renamed unobtainium.rule to lowercase. rizzyou.txt updated to 223 words. |
| 2026-02-27 | unobtainium.rule updated to 258 rules (year+special suffix patterns, batch-0023 feedback). nocap-plus.txt updated (+81 roots from feedback loop). Expanded complementary attacks section with measured hybrid ROI data and digit+special suffix attacks. Added hybrid speed note. |
| 2026-03-24 | bussin.rule expanded to 38 rules (keyboard walks promoted from unobtainium, double-digit appends, single-digit prepends). nocap.rule and nocap.txt rebuilt. rizzyou.txt updated to 223 words. |
| 2026-02-25 | bussin.rule audit — stripped 65 spacing-variant duplicates from original. nocap.rule rebuilt with space-normalized dedup. unobtainium.rule expanded to 248 rules (keyboard suffixes, prepends, special combos) |
| 2026-02 | Initial release — nocap.txt, nocap-plus.txt, nocap.rule, unobtainium.rule |

## Acknowledgments

- [rockyou.txt](https://en.wikipedia.org/wiki/RockYou#Data_breach) — The 2009 RockYou breach that gave the community its foundational wordlist
- [OneRuleToRuleThemAll](https://github.com/NotSoSecure/password_cracking_rules) by NotSoSecure — The original combined hashcat rule set
- [OneRuleToRuleThemStill](https://github.com/stealthsploit/OneRuleToRuleThemStill) by stealthsploit — Updated and deduplicated OneRule successor
- [HIBP Pwned Passwords](https://haveibeenpwned.com/Passwords) by Troy Hunt — Breach frequency data used for validation and testing

## License

MIT License — see [LICENSE](LICENSE).

## Author

Seth Holcomb ([@doritoes](https://github.com/doritoes))
