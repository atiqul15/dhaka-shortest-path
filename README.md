# Shortest Path in Dhaka: A* vs Bidirectional Dijkstra vs Hill Climbing

Finding a route between two places in Dhaka using real road data from OpenStreetMap, and comparing three search algorithms on the same road network.

The road map is treated as a graph: every road junction is a **node**, and every road piece is an **edge** weighted by its length in metres.

## Algorithms compared

| Algorithm | Idea | Shortest path? |
|---|---|---|
| **A\* Search** | Expands the node with the lowest `f = g + h`, where `g` is the distance travelled and `h` is the straight-line (haversine) distance to the goal. | Yes |
| **Bidirectional Dijkstra** | Two searches, one forward from the source and one backward from the destination, until they meet. No heuristic. | Yes |
| **Hill Climbing** | Always moves to the unvisited neighbour closest to the goal and never goes back. Can get stuck in dead ends. | No (and it can fail) |

## Dataset

- **Source:** OpenStreetMap data for Bangladesh from [Geofabrik](https://download.geofabrik.de/asia/bangladesh-latest.osm.pbf) (`bangladesh-latest.osm.pbf`, about 340 MB).
- **Area used:** Dhaka only, cut out with the bounding box `[90.30, 23.65, 90.55, 23.90]` (west, south, east, north).
- **Network type:** driving roads, read with `pyrosm` and converted to a directed `networkx` graph.
- The dataset file is **not** included in this repo because of its size. The notebook downloads it automatically (or you can upload it / load it from Google Drive).

## How it works

1. Load the `.osm.pbf` file and extract the Dhaka driving network.
2. Build a directed graph (nodes = junctions, edge weight = length in metres).
3. Snap the source and destination coordinates to the nearest junction using a `BallTree` (haversine metric).
4. Run the three algorithms on the same source and destination.
5. Draw each algorithm's route on its **own separate map**.
6. Test all three on 20 random routes (at least 3 km apart, from the largest strongly connected part of the graph) and compare success rate, extra distance versus the shortest path, execution time and nodes explored.

## How to run

**Option 1: Google Colab (recommended)**

1. Upload `shortest_path_dhaka.ipynb` to [Google Colab](https://colab.research.google.com/) (or use File > Open notebook > GitHub).
2. Run all cells. The first cell installs the libraries, and `DATA_SOURCE = "download"` fetches the dataset.
3. To try other places, change the four coordinates in Step 3 (`source_lat`, `source_lon`, `destination_lat`, `destination_lon`).

**Option 2: Locally**

```bash
pip install -r requirements.txt
jupyter notebook shortest_path_dhaka.ipynb
```

(Remove the `from google.colab import files` lines in the notebook, since they only work in Colab.)

## Output files

Running the notebook generates:

| File | Content |
|---|---|
| `map_astar.html` | Route found by A* (blue) |
| `map_bidirectional_dijkstra.html` | Route found by Bidirectional Dijkstra (purple) |
| `map_hill_climbing.html` | Route found by Hill Climbing (orange, or a red dashed partial path if it gets stuck) |
| `shortest_path_comparison.csv` | Single-route comparison table |
| `multi_route_summary.csv` | Averages over the 20 random routes |
| `multi_route_trials.csv` | Per-trial results |
| `algorithm_results.json` | Single-route results in JSON |

Open the `.html` maps in any browser.

## Results

> Fill this in after running the notebook.

**Single route (Mirpur to Old Dhaka):**

| Algorithm | Status | Distance (km) | Time (s) | Nodes explored |
|---|---|---|---|---|
| A* Search | | | | |
| Bidirectional Dijkstra | | | | |
| Hill Climbing | | | | |

**Average over 20 random routes:**

| Algorithm | Success rate (%) | Avg extra distance (%) | Avg time (s) | Avg nodes explored |
|---|---|---|---|---|
| A* Search | | | | |
| Bidirectional Dijkstra | | | | |
| Hill Climbing | | | | |

## Notes and limitations

- Edge weights are road length only. Speed limits, traffic and turn restrictions are not modelled.
- Hill Climbing uses only straight-line distance to the goal, so it fails on dead ends, rivers and one-way streets.
- Execution times depend on the machine. Compare algorithms using the same run.

## Tech stack

Python, pyrosm, networkx, folium, scikit-learn, pandas, numpy, matplotlib.
