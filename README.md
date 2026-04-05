# Balcony Solar Panel Evaluator

An interactive simulator for evaluating small stepped solar panels on a balcony railing — comparing shading losses, protrusion tradeoffs, and yearly energy output across different configurations.

**[Try it live →](https://virgil-ideas.github.io/balcony-solar-panels-evaluator/)**

## What it does

Balcony solar setups face a core tension: tilting panels to catch more sun means they stick out further and shade the row below. This tool lets you explore that tradeoff visually.

You can adjust:

- **Month** — see how sun elevation changes shading throughout the year (high summer sun = more inter-row shading, not less)
- **Stepped pyramid offset** — push lower rows progressively further from the railing to escape the shadow of the row above
- **Balcony orientation** — compass direction affects total yield for all configurations equally

The simulator compares three setups side by side:

- **Stepped small panels** at 30° tilt (your config, adjustable via slider)
- **Single large panel** at 15° tilt (realistic single-panel install, shown as yellow ghost)
- **Vertical panel** flush against the railing (zero protrusion baseline, shown as green ghost)

## Key insights

- Inter-row shading is worst in **summer** when the sun is high — the steep rays hit the protruding panel lips and cast long shadows straight down the railing
- In **winter**, shallow rays sneak under the lips with minimal shading
- The **stepped pyramid** approach lets each row clear the shadow of the row above without the entire setup protruding 50cm
- Small panels at 30° tilt can **match or beat** a single large panel at 15° tilt, because the better angle compensates for shading losses

## Tech

Single HTML file, zero dependencies. Uses canvas for the side-view visualization with smooth animated transitions between states.

## License

Apache 2.0
