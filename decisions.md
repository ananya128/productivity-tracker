📘 DECISIONS.md

This document outlines the rationale behind the major technical decisions made while building the Productivity Tracker App using React, FastAPI, and GenAI. The goal was to design a modular, scalable, and intuitive system for daily task logging, visualization, GenAI-powered summaries, and vector-based historical search.

🔷 Frontend (React + Vite + TailwindCSS)

📌 Framework: React

React was chosen for its component-based architecture, flexibility, and strong ecosystem support. The app heavily relies on state-driven interactions, which are best managed using functional components and hooks.

⚡ Build Tool: Vite

Vite provides a faster and more modern development experience compared to Webpack. It supports hot module replacement (HMR) and rapid startup, making it ideal for frontend prototyping.

🎨 Styling: Tailwind CSS

Tailwind CSS allows utility-first styling and consistent UI theming. It enables quick prototyping of complex layouts (like heatmaps and bar charts) with minimal custom CSS.

🧩 UI Components

Shadcn/ui and custom components were used to structure task input forms, cards, tabs, and visualizations. These integrate well with Tailwind and provide a consistent design pattern.

📅 Date Handling: Day.js + date-fns

dayjs is used for weekly time windows, local date parsing, and 14-day time series.

date-fns handles calendar inputs and consistent date formatting for the task tab.

📈 Challenges Section Logic

Weekly tasks are filtered and parsed by date.

Tasks with focusLevel = high are tracked across days for streaks.

Completed tasks contribute to the 20-task goal.

Focus-weighted minutes are used to compute 8-hour focus goal progress.

The most productive day is calculated using task frequency.

Longest task duration is computed from time values.

📊 Productivity Graph Logic

Tasks are mapped to dayjs dates and assigned focus scores (low=3, med=6, high=9).

Buckets for each of the last 14 days accumulate tasks, minutes, and focus scores.

Final chart series includes tasks, hours, and average focus level.

Recharts was chosen for interactive, responsive visualization.

🗂 TaskList Tab Logic

Provides Today, Upcoming, Backlog, and Completed task views using Shadcn Tabs.

Supports CRUD operations on tasks: add, edit, delete, complete.

Focus levels are color-coded and editable inline.

LocalStorage is used for persistent task storage.

Sample JSON is loaded on first use to simulate realistic data.

tasksUpdated event ensures all visual components re-render on task state change.
