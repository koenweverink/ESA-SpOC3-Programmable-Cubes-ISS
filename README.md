# ISS Challenge Solver

This project implements a multi-stage approach to reconfigure an ensemble of cubes into the target ISS structure using pivoting maneuvers. The solver is organized into three main stages:

1. **Greedy Initialization:**  
   Quickly generates an initial solution from the provided initial configuration by iteratively selecting moves that maximize cube overlap and minimize distance to target positions.

2. **Iterative Lookahead Extension:**  
   Refines the greedy solution by "looking ahead" – sampling short sequences of moves (starting with 2 moves and increasing up to 5 if necessary) for cubes that are not yet correctly placed. The best candidate extension (i.e. the one that improves the UDP fitness by a minimum threshold) is appended to the solution.

3. **Final Polishing (BFS/A\* Search):**  
   A final refinement step is applied to reposition any remaining misplaced cubes. A BFS (or A\*-like search) is used to find short move sequences that reposition individual cubes (or small groups) into valid target positions. The global solution is updated if an improved configuration is found.

Additionally, an animation module is included to visualize the reconfiguration process from the initial configuration to the final solution.

---

## File Structure

- **Data Files:**  
  - `data/ISS/Initial_Config.npy`: Initial positions of cubes.  
  - `data/ISS/Initial_Cube_Types.npy`: Cube types for the initial configuration.  
  - `data/ISS/Target_Config.npy`: Target positions for cubes.  
  - `data/ISS/Target_Cube_Types.npy`: Cube types for the target configuration.

- **Problem Specification:**  
  - `problems/ISS.json`: Contains additional problem information (e.g. color definitions for visualization).

- **Solver Scripts:**  
  - The integrated solver (e.g. `final_solution_ISS.ipynb` or a Python script) implements all three stages of the solution.
  - Intermediate solutions are saved as:  
    - `greedy_solution.npy`  
    - `lookahead_solution.npy`  
    - `final_solution.npy` (or `final_greedy_refined_solution.npy` if using extra greedy refinement).

- **Animation:**  
  - The animation script (integrated in the solver) uses Matplotlib and Seaborn to create an animated GIF (saved as `ISS.gif`).

---

## How It Works

### 1. Greedy Initialization
A greedy algorithm builds an initial solution by testing all moves for each cube that is not yet correctly placed. Moves are chosen based on a weighted metric that favors increasing the number of cubes in their target positions (overlap) and reducing the total distance from misplaced cubes to their nearest valid target.  
*Output:* `greedy_solution.npy`

### 2. Iterative Lookahead Extension
Starting from the greedy solution, the algorithm refines the solution using a lookahead extension operator. The extension operator starts with a lookahead of 2 moves. If no improvement is found for 3 consecutive iterations, the lookahead horizon increases by one (up to a maximum of 5). The candidate move sequence that improves the overall UDP fitness by at least a set threshold is appended to the solution.  
*Output:* `lookahead_solution.npy`

### 3. Final Polishing (BFS Search)
A final polishing step is used to reposition any remaining misplaced cubes. For each cube not in a valid target position, a BFS is run (with a depth limit) to find a short move sequence that will correctly reposition the cube. If a valid sequence is found that improves the overall UDP fitness, it is appended to the global solution.  
*Output:* `final_solution.npy`

### Animation
The animation module replays the final solution move-by-move starting from the initial configuration. The sparse cube positions are converted into a dense 3D voxel grid, and Matplotlib (with Seaborn) is used to generate an animated GIF (saved as `ISS.gif`). Frame sampling (e.g. every 200th frame) can be adjusted to speed up the animation.

---

## Running the Solver

1. **Set Up Environment:**  
   Ensure you have Python 3.x installed along with the required libraries:
   - NumPy
   - Matplotlib
   - Seaborn  
   Also, ensure the `programmable_cubes_UDP` module is available in your project directory or Python path.

2. **Data Files:**  
   Place the required `.npy` files and `ISS.json` in the expected directories (`data/ISS/` and `problems/` respectively).

3. **Run the Solver:**  
   Execute the integrated solver script (e.g., via Jupyter Notebook or directly as a Python script):
   ```bash
   python final_solution_ISS.py
   ```

    This will generate the greedy solution, refine it with iterative lookahead extension, and polish it with BFS/A* search. The final solution is saved as `final_solution.npy`.

4. **Generate Animation:**
    Once the final solution is generated, the animation module replays the moves and saves an animated GIF (ISS.gif). Adjust frame sampling in the animation code if necessary.