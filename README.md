# wordforge

Modern password wordlists and rules to supplement `rockyou.txt`.

## The Problem

`rockyou.txt` was leaked in 2009 from the RockYou breach. Seventeen years later, it's still the default wordlist for password cracking — but the passwords people use have changed. Cultural references, multilingual phrases, leet-speak patterns, and modern slang are poorly represented in a list frozen in 2009.

**wordforge** provides research-driven supplements that capture what `rockyou.txt` misses.

## Wordlists

### rizzyou.txt

The core wordforge wordlist. Contains modern password roots discovered through:

- Markov chain phrase generation across English, Spanish, and French corpora
- Cultural and multilingual pattern mining (Turkish, Indian, Arabic, Slavic, Chinese Pinyin, Korean, Portuguese-Brazilian)
- HIBP Pwned Passwords frequency validation — every root confirmed in real-world breaches
- Leet-speak and number-substitution patterns (`ready2go`, `just4fun`)
- Media, gaming, music, and meme references

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
| Markov phrase roots | Algorithmically discovered compound words |

## Rules

### nocap.rule

Comprehensive rule set for broad password transformation coverage. Pair with wordlists for dictionary+rules attacks.

```bash
hashcat -m <hash_type> hashes.txt nocap.txt -r nocap.rule
```

### UNOBTAINIUM.rule

Surgical, high-value rule set derived from analyzing hundreds of thousands of cracked passwords. Each rule earned its place through measured crack contribution — no filler.

Includes digit prepends/appends, year suffixes, capitalize+suffix combos, leet substitutions, and special character patterns that produce disproportionate results.

Best paired with the larger wordlists:

```bash
hashcat -m <hash_type> hashes.txt nocap-plus.txt -r UNOBTAINIUM.rule
```

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
| `nocap-plus.txt` | `UNOBTAINIUM.rule` | High-value surgical cracks |
| `nocap-plus.txt` | `nocap.rule` | Broad coverage |
| `BETA.txt` | `nocap.rule` | Test new experimental roots |

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
hashcat -m 0 hashes.txt nocap-plus.txt -r UNOBTAINIUM.rule
```

## License

MIT License — see [LICENSE](LICENSE).

## Author

Seth Holcomb ([@doritoes](https://github.com/doritoes))
