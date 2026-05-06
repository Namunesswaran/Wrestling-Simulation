# 🤼 Wrestling Tournament Simulation

A C++ simulation of a collegiate wrestling conference season and post-season tournament. Schools compete across all weight classes over multiple seasons, with a seeded bracket tournament held at the end to crown individual weight-class champions.

---

## Features

- **8-school conference** with randomly generated rosters
- **13 NCAA-style weight classes**: 94, 106, 113, 120, 126, 132, 138, 145, 152, 167, 189, 220, 285 lbs
- **Ability-score based match simulation** using normal distributions for realistic, probabilistic outcomes
- **Full round-robin season** — every school faces every other school
- **Seeded post-season tournament** per weight class using an 8-wrestler bracket (with consolation rounds)
- **Conference standings** ranked by overall win percentage

---

## How It Works

### Schools & Wrestlers
Each of the 8 schools generates 12 wrestlers, one per weight class (randomly assigned, no duplicates within a school). Each wrestler is assigned an **ability score** drawn from a normal distribution (mean = 100, std dev = 15).

### Match Simulation
When two wrestlers compete, each draws a performance score from a normal distribution centered on their ability score. The higher score wins. The spread (sigma) widens when wrestlers are closely matched, adding realistic variance to upsets.

### Season
The conference runs a full **round-robin season** (each school vs. every other school). Results are tracked in:
- Individual wrestler win/loss records
- A 3D **advantage matrix** `[weightClass][school1][school2]` used for tiebreaking

### Tournament
After the season, a **post-season tournament** is held for each weight class:
- 8 wrestlers are **seeded** by win record (with head-to-head advantage as a tiebreaker)
- The bracket uses a standard **double-elimination-style** structure with consolation rounds
- A champion is declared for each weight class

---

## Build & Run

### Requirements
- C++11 or later
- A standard C++ compiler (g++, clang++, MSVC)

### Compile
```bash
g++ -std=c++11 -o wrestling WrestlingTournament.cpp
```

### Run
```bash
./wrestling
```

### Sample Output
```
Conference winner: the team of 400 school with the score of 0.583! Congrats!
And the winner in the weight class 1 is number 302
And the winner in the weight class 2 is number 705
...
```

---

## Project Structure

```
WrestlingTournament.cpp
│
├── weightClasses[]       – Array of 13 weight class thresholds
├── struct Record         – Wins/losses tracker
├── class Wrestler        – Individual wrestler with ID, weight, ability score, record
├── class School          – Roster of wrestlers; generates random lineup on construction
├── class Conference      – Holds all schools; runs the season and tracks advantage matrix
├── struct Comp           – Comparator for seeding wrestlers by record and head-to-head
├── class Match           – Simulates a single bout between two wrestlers
├── class Tournament      – Seeds and simulates the post-season bracket per weight class
└── main()                – Runs 2 seasons, prints conference winner, runs all tournaments
```

---

## Known Limitations & Potential Improvements

- **Seeding bug**: The `Comp` comparator uses `w1.id / 100` to index the advantage matrix, but wrestler IDs are structured as `(schoolIndex+1)*100 + wrestlerIndex+1`. This can cause off-by-one indexing into the matrix. Replacing it with a dedicated school-index field would be more robust.
- **`bestScore` uninitialized**: The `Conference::bestScore` field is not initialized, which can cause undefined behavior when `bestSchool()` is first called. Initializing it to `0.0f` in the constructor fixes this.
- **Weight class 94**: Wrestlers below 94 lbs are never assigned a valid weight class in the current loop logic, as the loop starts at index `j=1`. This edge case can leave the lightest class unpopulated.
- **No named schools**: Schools are identified by integer multiples of 100. Adding string names would improve readability.
- **Single-file design**: The project could be split into header/source files for larger-scale development.

---

## License

This project is provided as-is for educational and simulation purposes.
