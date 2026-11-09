# Project proposal

## 1. The problem and goal

(Explain the problem you want to solve and why it matters. Describe what you
want to improve, how you will measure improvement, and which limits or
requirements must be respected. A short example can help someone outside your
field understand the problem.)

**The problem and goal in my project:**

Ion-exchange membranes such as Nafion are characterized by broadband dielectric spectroscopy (1 MHz-1 GHz). The spectra are interpreted by fitting models, which give a relaxation time, a strength and a shape for each process. The difficulty is not running a fit but deciding which model to fit: which relaxation function (Havriliak-Negami, Cole-Cole, Cole-Davidson, Debye), how many processes, whether a conductivity contribution or an electrode-polarization term is also needed, which frequency window, and which weighting. Several choices fit almost equally well, and a model that fits well can still have parameters the data cannot determine. Today I do this by hand, one spectrum at a time. For example, a single-relaxation fit of one Nafion spectrum looked acceptable by R squared but left structured residuals of 20-36% at the high-frequency end, and one shape parameter sat on its bound. That means the model was wrong, not just the fit.

Improvement is measured in three ways. On synthetic spectra where I know the generating model, how often the agent picks the correct elements and number of processes. On spectra I have fit by hand, how closely it agrees with me. And how often it wrongly accepts an extra process in pure noise. The limits are that parameters stay within physical bounds, a model with a parameter on a bound or with unidentifiable parameters is flagged and never silently accepted, and the physical interpretation of a relaxation always stays with me.

## 2. The simulator

(Describe a program or model that could simulate your system and calculate what
happens under different conditions. Explain what inputs it takes and what
results it produces. State whether it can run on your computer and accept inputs
in a text file, such as JSON or YAML. If you have not chosen a simulator yet,
name an option you want to explore and explain what you need to check.)

**The simulator in my project:**

The ground is a deterministic Python fitter, not a time-stepping simulator. It combines a forward model of the dielectric spectrum with a least-squares fit. The model is assembled from optional elements: one or more relaxation functions (Havriliak-Negami, Cole-Cole, Cole-Davidson, Debye), a DC conductivity term, and a constant phase element (CPE) for electrode polarization (planned; the relaxation functions and the conductivity term are implemented). It takes a configuration file (YAML or JSON) that specifies which elements are included, the frequency window, the weighting, the parameter bounds, and the starting values. It returns the fitted parameters with uncertainties, the residuals, goodness-of-fit numbers, and diagnostics: which parameters hit a bound, how strongly parameters are correlated, and whether the residuals still show structure. It runs on my computer in seconds, has no GUI, and gives identical output for identical input. It already has tests: a comparison against my MATLAB reference output and a recovery test on synthetic data.

## 3. The agent's role

(Explain what the language-model-based agent will decide or change, and how it
will use the simulator's results to choose its next action. If known, list the
variables it may change and their allowed ranges. Explain what you expect the
language model to contribute: could a simple program that tries different
options and picks the best result achieve the same goal? Include one question
you still need to answer.)

**The agent in my project:**

The agent chooses what to try next. It reads the diagnostics from a fit and then changes the configuration: which relaxation family to use, the number of relaxations (1–2, possibly 3), whether to add or remove a conductivity contribution or a CPE, the frequency window, the weighting, and the starting values and bounds, restricted to physical ranges (for example, shape parameters between 0.2 and 1). It decides when to stop and recommends a model with a stated reason, and flags anything the data cannot determine. A human approves before it accepts more than one relaxation, a parameter on a bound, or unidentifiable parameters.

A typical decision: a rising low-frequency tail in the residuals can mean a missing conductivity or electrode-polarization term and not a missing relaxation. Telling these apart, and saying why, is the judgment I want from the agent.

A simple program could try every combination and pick the lowest information criterion, and for clean data that would work. I expect the language model to add value where that fails: reading structured residuals to decide which kind of element is missing, noticing that a high-frequency artifact (not a real process) is driving the fit so the window should change, and explaining a recommendation or a refusal to choose. I will test this by comparing the agent against that brute-force baseline, and if the baseline wins, I will say so in the report.

## Explain your project to someone outside your field

(Now describe your whole project again, as if you were explaining it to someone
from another field who has no background in your topic. For example, if you
study chemical engineering, imagine explaining it to a physicist; if you study
aeronautical engineering, imagine explaining it to someone in medicine; if you
study data science, imagine explaining it to a materials engineer. In one or two
short paragraphs, connect all three parts: the problem you want to solve, how
the simulator helps you test ideas, and what the agent will decide or change.
Use everyday language, explain any technical terms you need, and avoid
unexplained abbreviations. Do not simply copy your answers above.)

**My project in simple words:**

I study plastic-like membranes that let charged particles pass, the kind used in fuel cells and water treatment. To learn how those particles move, I put the membrane in an electric field that oscillates very fast and measure how it responds. The response curve contains several overlapping "wiggles", each telling me how fast one part of the material can move, plus extra effects from the electrodes and from charges drifting through the membrane. To read them, I fit mathematical curves to the data. The hard part is that I have to guess which curves to use and how many, and different guesses can look equally good while meaning very different things physically. The program I already have takes a guess and says how well it fits, in a fraction of a second, and I can trust it because it is tested against known answers. The AI agent's job is to make the guesses: it looks at where the fit is still wrong, tries something different, and tells me which choice it recommends and how sure it is. I stay in charge of what the results mean. The agent only proposes, and it has to ask me before it accepts anything risky.
