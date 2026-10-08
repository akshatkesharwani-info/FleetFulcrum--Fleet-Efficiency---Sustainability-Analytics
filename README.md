# FleetFulcrum: Fleet Efficiency & Sustainability Analytics

Fuel cost, CO2 emissions and idling waste for a mixed vehicle fleet: which vehicles cost more than they should, what drives fuel use, and what an electric-vehicle replacement or an idling cut would save. An AI-written recommendation list turns the calculated scenarios into actions.

Built in Google Colab with Groq (`openai/gpt-oss-120b`).

> **The fleet data is made up and the cost and CO2 figures are assumptions.** 100 vehicles over 12 months were generated, and fuel prices, emission factors and idle-waste share are editable numbers at the top of the notebook. The notebook also accepts your own `fleet.csv`. The findings show the method, not a real fleet.

## What it does

1. **Cost and CO2 per vehicle:** cost per km, CO2 per km and idle waste for every vehicle, then by vehicle type.
2. **Flags vehicles that cost more than their own type.** Each vehicle is compared with others of the **same type** (z-score above 1.5), because comparing an electric van with a diesel truck says nothing.
3. **Finds the drivers of cost** inside the diesel trucks with correlations and a model, tested by 5-fold cross-validation **by vehicle** (each vehicle appears 12 times, so a plain split would leak).
4. **What-if scenarios:** replace old diesel vehicles with electric ones, halve idling, and bring the expensive vehicles down to their type's median.
5. **Sensitivity tables** for the electric-vehicle price and the electricity price.
6. **AI recommendations (Groq).** All scenario numbers are calculated in code. The AI only explains them: it must not calculate new numbers, add currency symbols or call the scenarios re-pricing, and it must base advice on correlations when the model is weak.

## Results from the run (made-up data)

| Measure | Result |
|---|---|
| Fleet | 100 vehicles: 32 diesel trucks, 28 petrol vans, 18 hybrid cars, 11 CNG buses, 11 electric vans |
| Fleet CO2 / fuel cost | 2,054.8 tonnes / 71,365,880 |
| Idle waste | 5,863,714, which is 8.2% of fuel cost (using an assumed waste factor) |

**By vehicle type (cost per km, kg CO2 per km):** Diesel Truck 34.85 / 0.93, CNG Bus 17.23 / 0.48, Petrol Van 14.48 / 0.26, Hybrid Car 10.44 / 0.16, Electric Van 5.82 / 0.18. These gaps are mostly set by the assumed fuel prices and emission factors.

**Inside the diesel trucks (the more useful finding):**
- Correlation with cost per km: **vehicle age 0.72**, driver score -0.46, idle % 0.36.
- The worst 10% of diesel trucks cost 1.2 times more per km than the best 10%.
- 7 vehicles in the fleet cost clearly more than their own type (up to 2.5 standard deviations above it).
- Fuel-use model, 5-fold R2 by vehicle: Linear Regression **0.515**, Random Forest 0.318. The simple model wins, so it ranks the drivers: a 1-standard-deviation increase in age adds 1.28 litres per 100 km, idle % adds 1.01, driver score changes it by -1.11. R2 of about 0.5 means the ranking is only a hint.

**Scenarios:**

| Scenario | Yearly saving | CO2 cut |
|---|---|---|
| Replace the 15 diesel vehicles aged 7+ with electric ones | 21,077,848 | 537.1 t (26.1% of fleet CO2) |
| Halve idling | 2,931,857 | 84.5 t |
| Bring the 7 expensive vehicles down to their type's median cost per km | 1,106,999 | not calculated |

**Payback of the electric replacement** depends on the vehicle price: 1.8 years at the assumed 2.5 million each, 2.8 years at 4 million, 4.3 years at 6 million and 5.7 years at 8 million. The electricity price barely matters here (the yearly saving moves from 21.08 million at 9 per kWh to 20.28 million at 15).

## What the evaluation showed

- **Compare vehicles with their own type.** The earlier approach flagged two electric vans as "unusual" only because they differ from diesel trucks.
- **The simplest model won.** With only 32 diesel vehicles, the Random Forest overfit, so Linear Regression gave the steadier and more readable ranking.
- **The headline saving needs a price warning.** A 1.8-year payback becomes 5.7 years if the electric vehicle costs about three times as much, and the notebook shows both.
- **AI recommendations needed guard rails.** An earlier version recommended "re-pricing" vehicles, compared them with the fleet average instead of their type, and built advice on a weak model.

## Limitations

- **Made-up data and assumed constants.** Do not quote these figures as a real fleet's performance.
- The scenario assumes an electric van can do a diesel truck's job, which may not be true for heavy loads.
- The idle waste figure uses an assumed share of fuel burned while idling.
- Only 32 diesel trucks, so the model scores are noisy (R2 spread of about 0.13 across folds).
- Correlations show links, not causes.

## Tech stack

pandas, scikit-learn, matplotlib, seaborn, Groq API.

## How to run

1. Open the notebook in Google Colab.
2. Run the cells from top to bottom.
3. When asked, paste your own Groq API key (free at console.groq.com). It is hidden while typing and is never saved in the notebook.

## Files the notebook creates

- `fleetfulcrum_vehicles.csv`: one row per vehicle with cost, CO2, idle waste and flags
- `fleetfulcrum_monthly.csv`: monthly fuel, cost and CO2 per vehicle

---

Built by **Akshat Kesharwani** | [GitHub](https://github.com/akshatkesharwani-info) | [Portfolio](https://akshatkesharwani-info.github.io/)
