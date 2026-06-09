# 🗳️ Voting Assistant Agent

## Overview

An AI agent that helps voters make informed decisions by analyzing candidate alignment with their priorities. This project demonstrates practical AI application through pattern-based reasoning, multi-step agentic workflows, and real-world problem-solving.

Built as a solution to a personal need during the 2026 California primary elections.

---

## Quick Start

```bash
# Clone and setup
git clone https://github.com/ngdias-sys/voting-agent.git
cd voting-agent
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
python3 -m pip install -r requirements.txt

# Configure API keys
cp .env.example .env
# Edit .env and add your ANTHROPIC_API_KEY

# Run the agent
python3 voting_agent.py
```

---

## The Problem

**The Challenge:** Voters spend 6+ hours researching candidates across fragmented sources and still feel uncertain about their choices.

**The Cost:**
- Information overload: Too many sources, conflicting information
- Decision fatigue: Hard to synthesize candidate positions across multiple issues
- Low confidence: "Did I make the right choice?"
- Low participation: Intimidation factor keeps some voters from researching at all

**Why It Matters:** This is a real operational problem. Not just for voting—for any decision under uncertainty.

---

## The Solution

**Input your political priorities** → **Agent analyzes candidates** → **Get personalized ranking with detailed reasoning**

The agent doesn't replace human judgment. It removes friction and builds confidence in decision-making.

```
┌─────────────────────────────────────────────────────────────┐
│                    VOTING AGENT WORKFLOW                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  LEARN                ANALYZE              EXPLAIN          │
│  ──────               ───────              ───────          │
│  • 3 issues    →      • Claude scores   →  • Text report    │
│  • Positions         • 0-100 per issue     • HTML guide     │
│  • Location          • Reasons             • Visual ranking │
│                                                             │ 
└─────────────────────────────────────────────────────────────┘
```

---

## Key Features

✅ **Priority Learning**  
Captures your top 3 political issues and your positions on each one.

✅ **Candidate Analysis**  
Uses Claude API to score candidate alignment 0-100 on each of your priorities. Reasoning is explicit, not hidden.

✅ **Multi-Format Output**  
Generates both text reports (detailed, exportable) and beautiful HTML voting guides (visual, shareable).

✅ **Transparent Reasoning**  
Explains *why* each candidate aligns with you, not just *what* the score is.

---

## Project Structure

```
voting-agent/
├── voting_agent.py              # Main agent class with all methods
├── candidates.json              # 2026 CA Governor candidates + positions
├── requirements.txt             # Python dependencies
├── .env.example                 # API key template
├── sample_output_1.html         # Example: SF voter (polarized)
├── sample_output_2.html         # Example: SD voter (moderate)
└── README.md                    # This file
```

---

## Architecture

**Built With:**
- **Language:** Python 3.14+
- **LLM Engine:** Claude API (claude-sonnet-4-6)
- **Framework:** Class-based agent with multi-step workflow
- **Output Rendering:** Markdown → HTML conversion

**Core Components:**
1. **VotingAgent Class** - Orchestrates the entire workflow
2. **learn_preferences()** - Captures user priorities via input
3. **search_candidates()** - Loads candidate data from JSON
4. **score_alignment()** - Claude API analyzes and scores candidates
5. **generate_report()** - Creates text voting guide
6. **generate_html_report()** - Creates visual HTML guide

**Why These Choices:**
- **Claude (not GPT):** Better at nuanced policy reasoning; aligned with production use case
- **Class-based structure:** State management across multi-step workflow
- **Local JSON (v1):** Simple, reliable, no API dependencies for candidate data
- **Markdown → HTML:** Client-side processing, consistent styling

---

## How to Run

### Prerequisites
- Python 3.14+
- Anthropic API key ([get one here](https://console.anthropic.com))
- SERPAPI key (optional, not currently used)

### Setup
```bash
# Clone the repo
git clone https://github.com/ngdias-sys/voting-agent.git
cd voting-agent

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
python3 -m pip install -r requirements.txt

# Configure API keys
cp .env.example .env
# Edit .env and add your ANTHROPIC_API_KEY

# Run the agent
python3 voting_agent.py
```

### What Happens
1. Agent prompts for your top 3 political issues
2. For each issue, asks for your position
3. Prompts for your location
4. Loads candidate data (2026 CA Governor race)
5. Analyzes alignment using Claude API (~15 seconds)
6. Generates text report (terminal output + saved .txt file)
7. Generates HTML report (saved .html file)
8. Done!

---

## Results & Validation

**Metrics Tested:**
- ✅ **Consistency:** Ran 4 test scenarios with different priorities and locations. Output format consistent, reasoning quality high.
- ✅ **Accuracy:** Scores are logically defensible. Candidate positions correctly interpreted and scored.
- ✅ **Alignment Accuracy:** Tom Steyer 92%, Katie Porter 89%, Xavier Becerra 82%, Steve Hilton 5%, Chad Bianco 4% (based on progressive voter priorities)
- ✅ **Time Savings:** Agent produces in-depth analysis in 15 minutes vs. 6 hours of manual research

**Example Output:**
- Input: 3 priorities (climate, healthcare, education)
- Processing: 15 seconds (Claude API call)
- Output: 
  * Text report: 2-3 pages of detailed analysis
  * HTML report: Interactive visual guide with rankings + reasoning
  * Files saved: `voting_guide_[location].txt` and `.html`

**See live examples:**
- [Sample Output 1](https://github.com/ngdias-sys/voting-agent/blob/main/sample_output_1.html) - Left-leaning voter (polarized priorities)
- [Sample Output 2](https://github.com/ngdias-sys/voting-agent/blob/main/sample_output_2.html) - Moderate voter (mixed priorities)

---

## Technical Decisions & Trade-offs

**Decision 1: Claude API for Reasoning**
- Why: Nuanced policy analysis requires judgment, not keyword matching
- Trade-off: Slower than simple scoring; costs money per API call
- Worth it: Accuracy matters more than speed for this use case

**Decision 2: Local JSON Data (v1)**
- Why: Simplicity, reliability, no external data dependencies
- Limitation: Manual data entry; doesn't scale to other elections
- v2 Plan: Live API integration (Ballotpedia)

**Decision 3: Markdown → HTML Conversion**
- Why: Clean separation of concerns; styling handled in Python
- Limitation: Minor formatting variations between runs (acceptable for v1)
- v2 Plan: JSON-templated output for deterministic formatting

**Decision 4: Python Class-Based Structure**
- Why: Multi-step workflow; state management; extensibility
- Benefit: Easy to add methods (different report types, new analyses)
- Scalability: Pattern works for any decision-under-uncertainty problem

---

## What I Learned Building This

**Data Quality is Critical**  
The hardest part wasn't the reasoning engine—it was finding accurate, consistent candidate position data. In production, you need a single source of truth. This taught me that sometimes the constraint isn't the AI; it's the data.

**Formatting Consistency Matters**  
Claude generates fresh analysis each run, which means formatting varies slightly. For v1, that's acceptable. For production, you'd template the output (JSON structure) to guarantee consistency.

**Adoption Beats Features**  
The real value isn't "automation" alone. It's removing friction (fast) + building confidence (reasoning) + making it accessible (non-experts can use it). That's change management, not just tech.

**The Pattern is Universal**  
This voting problem has the exact same structure as a hiring decision, a purchasing decision, or a strategic choice: Unclear priorities → Too much information → Uncertain outcome. Once you solve it once, the pattern applies everywhere.

---

## v2 Roadmap

If this were production, here's what's next:

**Data Integration**
- Live API: Pull candidate positions from Ballotpedia or official election sources
- Real-time updates: Reflect new information, polling changes
- Validation: Cross-reference multiple sources for accuracy

**Output Consistency**
- JSON-templated responses: Claude returns structured data, not prose
- Fixed HTML template: Pixel-perfect consistency across all runs
- PDF generation: In addition to HTML and text

**User Experience**
- Priority discovery: Help users articulate what they care about
- User profiles: Save preferences, compare to previous elections
- Interactive dashboard: Visual comparison tools, candidate match charts

**Learning & Improvement**
- Feedback loops: Users validate/correct scores
- Accuracy tracking: Measure against actual election results
- Progressive learning: Agent improves recommendations over time

**Measurement**
- Time saved: Track hours researched vs. hours with agent
- Decision confidence: Before/after metrics
- Recommendation accuracy: Did users vote per agent's ranking?
- Adoption: Repeat usage, sharing behavior, non-voter participation

**Scalability**
- Multi-election: Any U.S. election (Senate, House, local)
- International: Adapt to different political systems
- Custom workflows: Template for other decision-under-uncertainty problems

---

## How This Applies Beyond Voting

The underlying pattern (Learn priorities → Analyze options → Rank → Explain) works for:

- **Sales:** Lead qualification and prospecting
- **Hiring:** Resume screening and candidate matching
- **Procurement:** Vendor selection and comparison
- **Strategy:** Strategic options analysis and recommendations
- **Operations:** Resource allocation and prioritization

This project is a proof-of-concept for that pattern.

---

## Technical Notes

**Environment Variables:**
```
ANTHROPIC_API_KEY=your_key_here
```
No other keys required (SERPAPI not currently used).

**Dependencies:** See `requirements.txt`. Main packages:
- anthropic (Claude API client)
- python-dotenv (environment variable management)
- re (markdown parsing)

**Tested On:**
- Python 3.14.5
- macOS
- claude-sonnet-4-6 model

---

## Contact & Next Steps

**Questions?** Feel free to open an issue or reach out.

**Interested in this pattern?** This project demonstrates how AI can help humans make better decisions through structured reasoning and transparency. If you're working on similar problems, the framework is here.

**Want to contribute?** Pull requests welcome. Focus areas: better candidate data, improved HTML styling, feedback mechanisms for learning.

---

**Author:** Natasha Dias  
**Context:** Digital Transformation Partner @ PortPro | Built during 2026 CA Primary  
**Date:** June 2026  
**GitHub:** [ngdias-sys/voting-agent](https://github.com/ngdias-sys/voting-agent)

---

## License

This project is open source. Use it for learning, reference, or as a starting point for your own projects.

---

**Last Updated:** June 2026
