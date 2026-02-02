# Random Trade, Uneven Outcomes

This project is a small, self‑contained simulation of random exchange in a closed economy. It visualizes how **microstates** (who holds how much) evolve over time, and how the **macrostate** (the histogram of holdings) converges toward a stable, unequal distribution even when everyone starts equal.

The interface contains two plots:

- **Wealth per Agent (Microstate):** a bar for each agent’s current currency.
- **Currency Histogram (Macrostate):** how many agents sit at each currency level.

You can step the simulation, run it continuously, and reset back to the initial state.

## What the code does

### Core model

The model is intentionally simple:

- `agentCount` agents exist, each with `currency` and `goods`.
- At each step, agents are randomly paired and attempt a trade.
- A trade is **1 unit of currency for 1 unit of goods**.
- Currency is conserved across the system (it only moves between agents).

Because goods are abundant (`startGoods` is very large), the main constraint is whether a buyer has any currency. This matches the classic random-exchange model where money moves but is conserved.

### Why this produces inequality

The system is closed and random. In such systems, there are **many more microstates** that correspond to unequal allocations than perfectly equal ones. The most probable macrostate is therefore not equality but a distribution with a long tail. In the classical limit, maximizing entropy under fixed total money yields an **exponential distribution**:

```
P(m) ∝ e^(−m/T)
```

where `T` is the average currency per agent.

The important point: macro inequality appears even though **no agent has a structural advantage**. The histogram stabilizes, but the identity of the “rich” agents continues to change as the microstate keeps reshuffling.

## How the simulation is implemented

All logic lives in `index.html` and runs in the browser.

### State and parameters

- `agentCount`: number of agents.
- `startCurrency`, `startGoods`: initial endowments.
- `agents`: an array of `{ currency, goods }` objects.
- `steps`: how many trade steps have occurred.

### Step mechanics

Each call to `step()`:

1. Shuffles agent indices.
2. Pairs agents and randomly chooses a buyer/seller order.
3. Attempts the trade if the buyer has currency and the seller has goods.

This keeps total currency constant while letting it move stochastically between agents.

### Rendering

Two canvases are used for fast rendering:

- `drawBars()` renders generic bar charts with gradients and grid lines.
- `drawHistogram()` bins the currency values and draws the macrostate.

The histogram also includes:

- Axis ticks aligned to the bin boundaries.
- A red dotted line marking the **initial wealth**, so you can see how the distribution drifts relative to the starting point.

### Controls

- **Step / Step x10:** manual evolution.
- **Play / Pause:** continuous evolution at the selected speed.
- **Reset:** restores initial values and zeroes the step count.
- **Speed slider:** controls the interval between steps (higher = faster).

## What to expect when you run it

- At step 0, the histogram shows a single spike at the initial currency value.
- After a few hundred to ~1000 steps, the distribution spreads and begins to resemble an exponential tail.
- The histogram stabilizes while the per‑agent bars keep changing, illustrating a stable macrostate with a dynamic microstate.

## Customize the experiment

Key parameters in `index.html`:

- `agentCount`
- `startCurrency`
- `startGoods`
- `histogramBinSize`
- `histogramBinCount`

If you reduce `startGoods` drastically, the distribution becomes **bounded and symmetric** (because agents can hit a goods ceiling). If you keep `startGoods` large, the distribution becomes closer to the textbook exponential.

## Run locally

No build step is required. Open `index.html` in any modern browser.
