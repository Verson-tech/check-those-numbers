# Lottery Ticket Checker

## Try it now (no installation)

**Open the app here: https://verson-tech.github.io/check-those-numbers/**

1. Pick **Powerball** or **Mega Millions** at the top.
2. Click the first box under "Your tickets" and type the numbers from your ticket. The cursor jumps to the next box on its own.
3. Press **Run to win** to see whether you won. Use **+** to add more ticket lines and **Reset** to start over.

It works in any browser on a phone, tablet or computer, and nothing you type is saved or sent anywhere.

---

A single-page web app that checks Powerball and Mega Millions tickets against the latest official drawing. It is built with plain HTML, CSS and JavaScript, with no frameworks, no build step and no backend.

Winning numbers load automatically from a free, publicly accessible API each time the page opens. You type your ticket numbers and the app tells you what you won.

## Features

- **Two games, two tabs:** Powerball and Mega Millions each have their own winning numbers, ticket lines and prize table.
- **Live winning numbers:** the latest drawing is fetched on page load (see [Data source](#data-source)).
- **Auto-advancing inputs:** click the first box and keep typing, and the cursor moves to the next box on its own. Backspace on an empty box goes back, and pasting a full line such as `5 17 28 44 61 9` fills every box.
- **Validation:** numbers outside the game's range, and duplicate white balls, are flagged in red.
- **Multiple tickets:** once a line is complete, **+** adds another line.
- **Run to win:** checks every complete line, highlights matched numbers, shows the prize for each line and a total.
- **Reset:** clears all lines and results on the current tab without reloading the page.
- **Offline fallback:** hardcoded numbers in the `GAMES` object are used if the API can't be reached.
- **Design:** AWS-style palette (navy and orange), responsive, with light and dark mode.

## Data source

Winning numbers come from the **New York State Open Data portal** (`data.ny.gov`), which publishes the results of the multi-state games sold in New York. The portal runs on the Socrata Open Data API (SODA).

| Game          | Dataset                                                                              | API endpoint                                  |
| ------------- | ------------------------------------------------------------------------------------ | --------------------------------------------- |
| Powerball     | [Lottery Powerball Winning Numbers: Beginning 2010](https://data.ny.gov/d/d6yy-54nr) | `https://data.ny.gov/resource/d6yy-54nr.json` |
| Mega Millions | Lottery Mega Millions Winning Numbers: Beginning 2002                                | `https://data.ny.gov/resource/5xaw-6ayf.json` |

The app asks for only the newest draw using SoQL query parameters:

```
https://data.ny.gov/resource/d6yy-54nr.json?$order=draw_date DESC&$limit=1
```

The fields used are `draw_date`, `winning_numbers` and, where present, `mega_ball` and `multiplier`. The code reads the numbers defensively, so it handles both a single string containing all six numbers and a separate Mega Ball field.

### Public API and licensing

- This is a **publicly accessible, open-data API**. It needs **no account, no API key and no authentication**, and the app calls it directly from the browser.
- The datasets are published by the State of New York on its official open data portal and are listed in the [Data.gov catalog](https://catalog.data.gov/dataset/lottery-powerball-winning-numbers-beginning-2010).
- The Data.gov listing notes that this is a non-federal dataset covered by terms of use different from Data.gov's own, so **check the current terms on the dataset page at [data.ny.gov](https://data.ny.gov)** before any commercial or high-volume use.
- Heavy traffic may be rate-limited. Socrata supports an optional free App Token for higher limits (see [data.ny.gov/developers](https://data.ny.gov/developers)).
- Lottery numbers are factual public records. Even so, this project is **not affiliated with, endorsed by or sponsored by** the New York Lottery, Powerball, Mega Millions, the Multi-State Lottery Association or Mega Millions Consortium. Game names are used only to describe what the app checks.

## Getting started

Most people should simply use the [hosted version](https://verson-tech.github.io/check-those-numbers/). To run your own copy, no installation is needed:

1. Download `lottery-checker-live.html`.
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari).
3. Choose a tab, click the first box and type your numbers.

The hosted version above runs on GitHub Pages. The file can also be hosted anywhere static files are served, such as Netlify, or Amazon S3 with CloudFront.

> Sandboxed preview environments often block requests to other domains. If the numbers don't load there, open the hosted link or the file locally. If the API can't be reached, you will see the fallback message instead.

## Configuration

All game settings live in the `GAMES` object at the top of the `<script>` block:

| Property                   | Purpose                                                        |
| -------------------------- | -------------------------------------------------------------- |
| `api`                      | Endpoint used to fetch the latest drawing                      |
| `max` / `bonusMax`         | Highest white ball number / highest bonus ball number          |
| `white` / `bonus` / `date` | **Fallback** numbers and label, used only if the API fails     |
| `prizes`                   | Prize for each match combination (`"whiteMatches+bonusMatch"`) |

Prize amounts are base prizes. Jackpots and some prize levels vary by drawing, so always confirm results and payouts on the official sites:

- Powerball: https://www.powerball.com
- Mega Millions: https://www.megamillions.com

## How a ticket is checked

For each complete line, the app counts how many of the five white balls appear in the winning white balls (order doesn't matter) and whether the bonus ball matches. It then looks up that combination, for example `"3+1"`, in the game's `prizes` table.

## Project structure

```
lottery-checker-live.html   # the whole app (published as index.html on GitHub Pages): HTML, CSS and JavaScript
README.md
```

## Possible improvements

- Cache the latest draw in a small serverless function (for example AWS Lambda with API Gateway) to avoid calling the public API from every visitor's browser.
- Add Power Play and Megaplier multiplier calculations.
- Add more games from the same open-data portal.
- Add automated tests for the prize-matching logic.

## Disclaimer

For entertainment and informational purposes only. Results are not guaranteed to be accurate or current. Always verify winning numbers and prizes with the official lottery before claiming or discarding a ticket.
