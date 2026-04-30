
  ---
  Intro to R — Data Science Platform, Garvan Institute

  For Students

  This course is an introduction to R and RStudio for researchers with no prior programming experience. By the end of the course you will
   be able to:

  - Navigate the RStudio interface and set up a project
  - Understand the core data types and structures in R
  - Load, explore, and manipulate tabular data
  - Produce publication-quality plots using ggplot2

  Course Contents

  - 1 - Setup of R and RStudio — Installing R and RStudio, navigating the interface, creating a project, and writing your first lines of
  code
  - 2 - Understanding Data Types — Variables, data types, vectors, lists, and matrices
  - 3 - Dealing with Data Frames — Loading and exploring dataframes, indexing, and subsetting
  - 4 - Data Manipulation — Filtering, selecting, and transforming data using dplyr
  - 5 - Data Visualisation — Creating and customising plots using ggplot2, and integrating basic statistical tests
  - Extras 1 - Advanced ggplot2 — Further visualisation topics for those who finish early or want to go further
  - Extras 2 - Automation in R — For loops and apply functions for repeating operations across data

  ---
  For Trainers

  This repository contains the source files for the Intro to R course website, maintained by the Data Science Platform.

  Repository Structure

  - content/ — one markdown file per section of the course
  - data/ — input data files used during the session
  - _config.yml — site settings including the navigation bar; update this when adding or renaming content pages
  - photos/ and img/ — images used throughout the course material

  Making Edits

  - Create a new branch before making changes — do not edit directly on gh-pages
  - Edit markdown files in a plain text editor or RStudio Source mode (not Visual mode, as this reformats HTML)
  - Push your branch and open a pull request for review before merging into gh-pages
  - The live website updates automatically once changes are merged into gh-pages

  Adding a New Section

  - Add a new markdown file to content/ following the existing naming convention
  - Add a corresponding entry to the navbar-links section of _config.yml
  - Add any data files needed to data/