# WISDOM
```
          _         _                 
__      _(_)___  __| | ___  _ __ ___  
\ \ /\ / / / __|/ _` |/ _ \| '_ ` _ \ 
 \ V  V /| \__ \ (_| | (_) | | | | | |
  \_/\_/ |_|___/\__,_|\___/|_| |_| |_|
```

A collection of quotes, shown at random at shell startup. One quote per
line in `quotes.txt`. Ships as plain data — no install script, no
dependencies — so you can wire it into whatever shell, prompt, or MOTD
system you like.

## File format

Each line is a single quote, read out verbatim — there's no parsing of
contents, so a "quote" is just whatever sits on one line. My own
entries happen to use typographic quotation marks (“ ”) with an
attribution at the end:

    “It is not that we have a short time to live, but that we waste a lot of it.” — Seneca

But that's just house style, not a requirement. The examples in this
README don't care what a line looks like — plain text, no attribution,
different dashes, all fine. Blank lines are skipped.


## Install

    git clone https://github.com/dropkick/wisdom ~/.config/quotes

Update:

    git -C ~/.config/quotes pull

## Usage

The file is the interface: `$HOME/.config/quotes/quotes.txt`. Everything
else is up to you. The examples below pick a random quote and skip
silently if the repo isn't installed.

### zsh

```zsh
WISDOM_FILE="$HOME/.config/quotes/quotes.txt"

if [[ -s "$WISDOM_FILE" ]]; then
  quotes=("${(@f)$(<"$WISDOM_FILE")}")
  quotes=("${(@)quotes:#}")          # drop blank lines
  if (( $#quotes )); then
    q="${quotes[$((RANDOM % $#quotes + 1))]}"
    print -P "%F{cyan}${q}%f"
  fi
fi
```

Drop that in `.zshrc` (or source it from a startup script).

### bash

```bash
WISDOM_FILE="$HOME/.config/quotes/quotes.txt"

if [[ -s "$WISDOM_FILE" ]]; then
  mapfile -t quotes < <(grep -v '^[[:space:]]*$' "$WISDOM_FILE")
  if (( ${#quotes[@]} )); then
    q="${quotes[RANDOM % ${#quotes[@]}]}"
    printf '\033[36m%s\033[0m\n' "$q"
  fi
fi
```

(`mapfile` needs bash 4+, standard on Linux; on macOS use `brew install
bash` or swap in `IFS=$'\n' read -d '' -ra quotes < "$WISDOM_FILE"`.)

### One-liner (any POSIX shell)

```sh
sed -n "$(( ($RANDOM % $(wc -l < ~/.config/quotes/quotes.txt)) + 1 ))p" \
    ~/.config/quotes/quotes.txt
```

### fortune-style wrap

For longer quotes, fold to a fixed width with an indent, as used in my
dotfiles welcome script:

```zsh
fold -s -w 68 <<< "$q" | sed 's/^/ /'
```

## In the wild

Used by [dropkick/dotfiles](https://github.com/dropkick/dotfiles) — its
welcome script displays a random quote at shell startup and silently
skips that bit if this repo isn't installed.

