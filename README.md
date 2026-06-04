# Pipeline Studio — LLM Observability & Pipeline Intelligence Frontend

## Overview

Pipeline Studio is a real-time observability dashboard for multi-stage LLM systems. It visualizes how complex language model pipelines behave internally, exposing latency, cost, failure modes, validation scores, retries, and semantic drift at each stage of execution.

The system is designed around a core idea: LLM applications should not behave like black boxes. Every transformation, decision, and failure should be observable, measurable, and replayable.

To demonstrate this, the system uses a controlled “cold email generation pipeline” as a case study workload. The email output is not the product itself — it is simply a structured task used to stress-test and visualize pipeline behavior under constrained generation, validation, and retry conditions.

The frontend is a React-based dashboard deployed on Vercel and connected to a FastAPI backend that executes and streams pipeline traces in real time.

---

## What This System Actually Does

Rather than focusing on email generation, the platform enables users to:

* Define structured problem specifications for LLM pipelines
* Attach runtime context variables that influence generation behavior
* Execute multi-stage LLM workflows with live streaming telemetry
* Observe each stage’s outputs, validation scores, and failure reasons
* Analyze retries, cost accumulation, token usage, and semantic drift
* Inspect historical executions and compare pipeline behavior across runs

The “email” is simply one instantiation of a constrained generation pipeline used to demonstrate observability mechanics.

---

## Core Architecture

### Routing & Authentication

* React Router with protected and guest route guards
* Session-based authentication (cookie-backed via FastAPI Users)
* OAuth integration with Google
* Persistent session hydration on app load

---

### State Management

**AuthContext.tsx**
Manages authentication lifecycle including login, logout, session recovery, and OAuth callback handling. Automatically synchronizes user state with backend sessions.

**AppContext.tsx**
Central runtime store for:

* Problems (pipeline definitions)
* Contexts (runtime variables)
* Execution results (emails / pipeline outputs)

Includes:

* Local persistence via localStorage
* Backend synchronization layer
* Normalization between frontend and backend schemas

**ThemeContext.tsx**
Manages dark/light mode with system preference detection and persistent storage.

---

## Page System

| Page             | Route         | Function                                |
| ---------------- | ------------- | --------------------------------------- |
| Dashboard        | /             | System overview, recent executions      |
| New Problem      | /problems/new | Define pipeline logic and constraints   |
| Problem Detail   | /problems/:id | Configure context and execute pipelines |
| Executions       | /emails       | History of pipeline runs                |
| Execution Detail | /emails/:id   | Full trace + validation breakdown       |
| Analytics        | /analytics    | Cost, latency, failure mode analytics   |
| Batch Import     | /batch        | Lead/import ingestion pipeline          |
| Login            | /login        | Authentication flow                     |
| Register         | /register     | User registration                       |

---

## Core Components

### Problem Definition System

A structured form system that defines a pipeline specification:

* Identity layer (problem framing)
* Failure mode specification
* Analytical constraints
* Solution generation rules

This defines how LLM pipelines behave under execution.

---

### Context System

Runtime variables that influence execution behavior:

* Industry, company size, decision actor
* Constraint tags (chip-based system)
* Free-form contextual augmentation

Context modifies pipeline execution dynamically without changing the underlying problem definition.

---

### Pipeline Execution Engine

A real-time execution orchestrator that:

* Connects to backend SSE stream (/api/run-stream)
* Receives stage-level telemetry in real time
* Updates UI per event (trace_update, complete, error)
* Maintains execution state across multiple stages

---

## Observability & Visualization Layer

### Live Pipeline Tracker

The primary observability interface displaying:

* Per-stage execution status
* Latency per stage
* Token usage (input/output)
* Cost accumulation
* Retry attempts per stage
* Failure reasons
* Full retry history per stage

This acts as a distributed trace viewer for LLM workflows.

---

### Validation Dashboard

Displays structured evaluation scores for each pipeline stage:

* Semantic consistency
* Constraint adherence
* Drift detection
* Contradiction scoring

Metrics are normalized and visualized using threshold-based coloring.

---

### Email Viewer

Final output renderer for generated pipeline results:

* Subject/body separation
* Copy-to-clipboard
* Download as text file
* Email-client style formatting

---

### Pipeline Visualizer

Animated representation of pipeline execution used for loading and conceptual flow visualization.

---

## API Integration Layer

### Authentication

* Cookie-based session authentication
* Google OAuth redirect flow
* Structured backend error mapping

---

### Core API Endpoints

* POST /api/problems → create problem
* GET /api/problems → fetch problems
* POST /api/contexts → create context
* GET /api/contexts → fetch contexts
* POST /api/run → synchronous execution
* POST /api/run-stream → streaming execution
* GET /api/analytics → system analytics

---

### Streaming Architecture

Uses Server-Sent Events (SSE) for real-time execution tracing:

Event types:

* trace_update → stage-level updates
* complete → final output
* error → execution failure

---

## Design Philosophy

### Observability-First System Design

Every LLM action is explicitly visible:

* No hidden inference steps
* No silent retries
* No hidden cost accumulation

---

### Dark Infrastructure UI

* Dark-first interface optimized for dense telemetry
* Gradient-based stage identification
* Monospace metrics for cost/token precision
* Glass-style cards for layered system state

---

### Distributed Systems Mental Model

The frontend mirrors distributed tracing systems:

* Each pipeline stage behaves like a service span
* Each retry is an event in a trace tree
* Each execution is a full trace object

---

## Data Flow (Execution Lifecycle)

User defines problem → stored in backend
User attaches context → runtime configuration created
User starts execution → SSE stream begins
Backend emits stage events → UI updates in real time
Validation engine scores stages → dashboard updates
Final output rendered → stored locally + in app state

---

## Key Engineering Decisions

* SSE chosen over WebSockets for simplicity and proxy compatibility
* localStorage used for execution history caching
* Backend intentionally stateless for output persistence
* Runtime normalization layer between backend and frontend models
* Component-level state management for concurrent pipeline stages
* Debug visibility exposed in UI for trace-level debugging

---

## Tech Stack

Frontend:

* React + TypeScript
* Tailwind CSS
* React Router

Backend:

* FastAPI
* Server-Sent Events (SSE)
* Pydantic validation system

Deployment:

* Vercel (frontend)
* Separate FastAPI backend service

---

## File Structure

App.tsx → Routing, auth guards, layout
main.tsx → React root + providers
index.css → Global styles + Tailwind setup

Pages:
HomePage.tsx → Dashboard
NewProblemPage.tsx → Problem creation
ProblemDetailPage.tsx → Context + execution
EmailsPage.tsx → Execution history
EmailDetailPage.tsx → Execution viewer
AnalyticsPage.tsx → Metrics dashboard
BatchImportPage.tsx → Import pipeline
LoginPage.tsx / RegisterPage.tsx → Auth flow

Components:
Layout.tsx → App shell
ProblemForm.tsx → Problem builder
ContextForm.tsx → Context builder
PipelineRunner.tsx → Execution controller
LivePipelineTracker.tsx → Real-time trace UI
PipelineVisualizer.tsx → Animated flow
EmailViewer.tsx → Output renderer
ValidationDashboard.tsx → Score visualization

State:
AuthContext.tsx → Authentication state
AppContext.tsx → Core application state + sync
ThemeContext.tsx → UI theme management

API:
client.ts → API layer + SSE streaming + error handling
