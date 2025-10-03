# Capmetro Run-Cutting Problem

Problem Statement: Operator Shift Assignment for Transit Block Coverage

## Context

I am working on a shift assignment optimization project with CapMetro, Austin’s public transit agency. The system organizes daily service into “blocks,” where each block is a pre-defined sequence of trips (segments) that covers a specific route over the course of a service day. For example, a block for Route 1 may include multiple trips throughout the day, starting and ending at different timepoints. Some blocks span over 18 hours, making it impossible to assign a single operator to the full block due to labor and legal constraints.

The goal is to split and assign these blocks to multiple legal-length operator shifts, ensuring that all segments (sub-blocks) are covered exactly once, while respecting regulatory, operational, and logistical constraints. This process is known as run-cutting.

## Data Preprocessing

To enable feasible assignment and optimization:

Each block is split into smaller segments, referred to within this project context as pieces, based on sequential timepoints. In our case, piece 1 represent the segment between Timepoint 1 and Timepoint 2 within a block, Piece 2 covers Timepoint 2 to Timepoint 3, and so on. These pieces serve as the atomic units of work that must be combined and assigned to an operator exactly once in the final solution.

## Constraints

Each operator shift (also referred to as a "duty") must satisfy the following:

## Shift Duration

Total duration must be between 8 and 11 hours.

Duration includes:

 - Work time: sum of the durations of assigned pieces

 - Travel time: time to drive from the garage (E 5th) to the first timepoint of the first piece

 - Pre-trip time: a fixed 15-minute allowance at the start of each shift

## Contiguity of Pieces

A shift must consist of contiguous timepoints within blocks.

If an operator covers more than one block segment within the shift, then:

 - The gap between segments must be at least 30 minutes, and no more than 4 hours.

 - This reflects the agency's policy for minimum viable breaks and maximum acceptable downtime between runs.

## Complete and Unique Coverage

All pieces must be assigned exactly once across all shifts.


## Data Sources

1) east_5th_garage_weekday_timepoints.csv

Contains the raw block and timepoint information for each segment operating from the E 5th garage.

Each row represents a piece, with fields such as:

 - Block ID

 - Time

Other metadata (e.g., route, trip ID)

2) all_duty_combinations.csv

Contains a pre-generated set of feasible operator shift combinations (i.e., "duties") based on the raw timepoint data.

Each row represents a candidate shift, including:

 - Sequence of pieces assigned in the shift

 - Total duration (including travel and pre-trip)

 - Gaps between runs

This dataset was generated through a combinatorial enumeration of feasible shifts under the constraints above.


# Optimization Goal

The ultimate objective is to select a subset of feasible combinations of pieces to form shifts from all_duty_combinations.csv such that:

Every piece in east_5th_garage_weekday_timepoints.csv is covered exactly once

All selected shifts satisfy the defined constraints

Total number of operator shifts must not exceed 220, which corresponds to the number of drivers allocated to cover weekday service originating from the E 5th Street garage.

Optionally (depending on solution variant), the objective could also:

 - Minimize total operator idle time

 - Minimize travel time (deadhead) back to the garage 
