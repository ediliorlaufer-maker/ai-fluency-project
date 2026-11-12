# Project proposal

Degree level: PhD

## 1. The problem and goal

(Explain the problem you want to solve and why it matters. Describe what you
want to improve, how you will measure improvement, and which limits or
requirements must be respected. A short example can help someone outside your
field understand the problem.)

**The problem and goal in my project:**

I study ion-exchange membranes used in fuel cells and water treatment. One way to learn how charged particles move inside them is to measure how the material responds to a rapidly oscillating electric field. The measurement gives a curve, and to understand it I fit mathematical models to it. Running a fit is easy. The hard part is choosing which model to fit, because several different models can match the same curve almost equally well while meaning different things physically. Today I make that choice by hand, one measurement at a time, which is slow and hard to compare across many models.
The goal is a system that tries different models on a measured curve, checks how well each one works, and recommends the most trustworthy one. I measure improvement by testing it on made-up curves where I know the right answer, and by comparing it with fits I have already done and trust. The limits are that the results must stay physically reasonable, the system must say so when the data cannot decide between models, and the scientific interpretation always stays with me.

## 2. The simulator
(Describe a program or model that could simulate your system and calculate what
happens under different conditions. Explain what inputs it takes and what
results it produces. State whether it can run on your computer and accept inputs
in a text file, such as JSON or YAML. If you have not chosen a simulator yet,
name an option you want to explore and explain what you need to check.)

**The simulator in my project:**

The simulator is a Python program that stands in for the real experiment's analysis. It is not a time-stepping simulation. Given a description of a membrane's measured response, it builds a mathematical curve out of a chosen set of ingredients (one or more characteristic "wiggles", a term for current flowing through the material, and, planned but not yet built, a term for effects at the electrodes) and adjusts the numbers inside it until the curve matches the measurement as closely as it can.
It takes a plain text file in YAML or JSON that says which ingredients to include, which part of the measured range to use, how to weight the points, and the limits and starting guesses for each number. It returns the fitted numbers with their uncertainty, how far the curve still misses the data, and warning signs: whether a number got stuck at its limit, whether two numbers are trading off against each other, and whether the leftover error still has a pattern.
It runs on my computer in seconds, needs no graphical interface, and gives the same output for the same input every time. It is already tested against results from my earlier MATLAB code and against made-up data where I know the right answer.

## 3. The agent's role

(Explain what the language-model-based agent will decide or change, and how it
will use the simulator's results to choose its next action. If known, list the
variables it may change and their allowed ranges. Explain what you expect the
language model to contribute: could a simple program that tries different
options and picks the best result achieve the same goal? Include one question
you still need to answer.)

**The agent in my project:**

The agent is the part that decides what to try next. After each fit, it looks at the result and asks: where does the model still disagree with the measurement, and what is the most likely reason? It then changes the setup and runs the fit again. The things it may change are:

1-the type of model, and how many separate effects it includes

2-extra ingredients, for example a term for electric current flowing through the membrane, or for effects at the electrodes

3-which part of the measured range is used, and how the points are weighted

4-the starting guesses for the numbers being fitted, which must stay within physically sensible limits

When it stops, it recommends one model, explains why, and says what it could not determine. Before accepting anything risky, such as a model with extra effects or a result pushed against its limits, it has to ask me.
A simple program could try every combination and pick the best score, and for clean data that works. I expect the language model to help where the choice depends on understanding the pattern of the mistakes. For example, a certain kind of leftover error can mean an ingredient is missing and not that another effect is needed, and an odd feature at the edge of the range can be a measurement artifact rather than real behavior. I will compare the agent against the try-everything program, and if the simple program does as well, I will report that.

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
