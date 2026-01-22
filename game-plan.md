# Das Duell um die Geld - Comprehensive Design & Implementation Plan

## Project Vision
Create an elegant, immersive digital dealer for the poker-trivia game "Das Duell um die Geld". The website will serve as the game master - presenting questions, dramatically revealing hints, and showing answers with casino-worthy flair.

---

## Design Philosophy

### Inspiration: Browser-Use.com + Poker Aesthetic
Combining the sleek, developer-focused dark theme of browser-use.com with the drama and excitement of a high-stakes poker game.

**Core Principles:**
1. **Dark & Dramatic** - Deep blacks create focus and tension
2. **High Contrast** - Critical information pops against the darkness
3. **Progressive Revelation** - Information unveils like cards being turned
4. **Tactile Feedback** - Every interaction feels satisfying (sounds, animations)
5. **Mobile-First** - Works perfectly on a phone held at the game table

---

## Color System

### Base Palette (Browser-Use Inspired)
```css
:root {
  /* Backgrounds */
  --bg-primary: #0a0a0a;      /* Deepest black - main background */
  --bg-secondary: #0d1117;    /* Card backgrounds, panels */
  --bg-elevated: #161b22;     /* Hover states, elevated surfaces */

  /* Text */
  --text-primary: #e6edf3;    /* Main text - high contrast */
  --text-secondary: #8b949e;  /* Muted text, labels */
  --text-muted: #484f58;      /* Very subtle text */

  /* Borders */
  --border-default: #30363d;  /* Standard borders */
  --border-muted: #21262d;    /* Subtle borders */
}
```

### Poker Accent Colors
```css
:root {
  /* Primary Accents */
  --poker-red: #ff4444;       /* Danger, hearts, diamonds */
  --poker-gold: #ffd700;      /* Premium, winning, highlights */
  --poker-green: #00c853;     /* Success, money, go */

  /* Glow Effects */
  --glow-red: rgba(255, 68, 68, 0.4);
  --glow-gold: rgba(255, 215, 0, 0.4);
  --glow-green: rgba(0, 200, 83, 0.4);

  /* Gradients */
  --gradient-gold: linear-gradient(135deg, #ffd700 0%, #ffaa00 100%);
  --gradient-card: linear-gradient(180deg, #161b22 0%, #0d1117 100%);
}
```

### Timer State Colors
```css
:root {
  --timer-safe: #00c853;      /* > 50% time remaining */
  --timer-warning: #ffab00;   /* 25-50% time remaining */
  --timer-danger: #ff4444;    /* < 25% time remaining */
}
```

---

## Typography

### Font Stack
```css
:root {
  --font-primary: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  --font-display: 'Inter', system-ui, sans-serif;  /* For headlines */
  --font-mono: 'JetBrains Mono', 'Fira Code', 'SF Mono', monospace;  /* For numbers/answers */
}
```

### Type Scale
```css
/* Headlines */
.title-main {
  font-size: 2rem;
  font-weight: 800;
  letter-spacing: -0.02em;
  text-transform: uppercase;
}

/* Question Text */
.question-text {
  font-size: 1.5rem;
  font-weight: 600;
  line-height: 1.4;
}

/* Hint Text */
.hint-text {
  font-size: 1.125rem;
  font-weight: 400;
  line-height: 1.5;
}

/* Answer (Dramatic) */
.answer-text {
  font-family: var(--font-mono);
  font-size: 3rem;
  font-weight: 700;
  color: var(--poker-gold);
}

/* Labels */
.label {
  font-size: 0.75rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--text-secondary);
}
```

---

## Component Design

### 1. Header
```
┌─────────────────────────────────────────────────────────┐
│  ♠ ♥  DAS DUELL UM DIE GELD  ♦ ♣                       │
│                                                         │
│  [🔊 Sound: ON]                    Frage 7 von 51      │
└─────────────────────────────────────────────────────────┘
```

**Styling:**
- Poker suit symbols (♠♥♦♣) as decorative elements
- Subtle gold underline or glow on title
- Sound toggle in top-left, question counter top-right
- Border-bottom: 1px solid var(--border-muted)

### 2. Question Card (Hero Element)
```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│                     FRAGE 107                           │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │                                                 │   │
│  │    Wie viele Sekunden hat eine Stunde?         │   │
│  │                                                 │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Styling:**
```css
.question-card {
  background: var(--gradient-card);
  border: 1px solid var(--border-default);
  border-radius: 16px;
  padding: 2rem;
  box-shadow:
    0 0 0 1px var(--border-muted),
    0 8px 32px rgba(0, 0, 0, 0.4);
  position: relative;
  overflow: hidden;
}

/* Decorative corner accents (like playing card) */
.question-card::before,
.question-card::after {
  content: '♠';
  position: absolute;
  font-size: 1.5rem;
  color: var(--text-muted);
  opacity: 0.3;
}
.question-card::before { top: 12px; left: 16px; }
.question-card::after { bottom: 12px; right: 16px; transform: rotate(180deg); }
```

### 3. Hint Cards (Revealable)
```
BEFORE REVEAL:
┌─────────────────────────────────────────────────────────┐
│  HINWEIS 1                                              │
│  ████████████████████████████████████                   │
│  [Click AUFDECKEN to reveal]                            │
└─────────────────────────────────────────────────────────┘

AFTER REVEAL:
┌─────────────────────────────────────────────────────────┐
│  HINWEIS 1                                        ✓     │
│  Es besteht aus zwei Halbzeiten.                        │
└─────────────────────────────────────────────────────────┘
```

**Styling:**
```css
.hint-card {
  background: var(--bg-secondary);
  border: 1px solid var(--border-default);
  border-radius: 12px;
  padding: 1rem 1.25rem;
  transition: all 0.3s ease;
}

.hint-card.hidden .hint-content {
  filter: blur(8px);
  user-select: none;
  color: var(--text-muted);
}

.hint-card.revealed {
  border-color: var(--poker-green);
  box-shadow: 0 0 20px var(--glow-green);
}

.hint-card.revealed .hint-content {
  filter: none;
  color: var(--text-primary);
}
```

**Reveal Animation:**
```css
@keyframes cardFlip {
  0% { transform: rotateY(0deg); }
  50% { transform: rotateY(90deg); }
  100% { transform: rotateY(0deg); }
}

.hint-card.revealing {
  animation: cardFlip 0.4s ease-in-out;
}
```

### 4. Answer Card (Maximum Drama)
```
HIDDEN:
┌─────────────────────────────────────────────────────────┐
│                       ANTWORT                           │
│           ████████████████████████████                  │
│                    [LOCKED]                             │
└─────────────────────────────────────────────────────────┘

REVEALED:
┌─────────────────────────────────────────────────────────┐
│                       ANTWORT                           │
│                                                         │
│                       3600                              │
│                                                         │
│                    ★ ★ ★ ★ ★                            │
└─────────────────────────────────────────────────────────┘
```

**Styling:**
```css
.answer-card.revealed {
  background: linear-gradient(135deg, #1a1a2e 0%, #0d1117 100%);
  border: 2px solid var(--poker-gold);
  box-shadow:
    0 0 30px var(--glow-gold),
    inset 0 0 60px rgba(255, 215, 0, 0.1);
}

.answer-value {
  font-family: var(--font-mono);
  font-size: 4rem;
  font-weight: 800;
  color: var(--poker-gold);
  text-shadow: 0 0 30px var(--glow-gold);
  animation: answerPulse 2s ease-in-out infinite;
}

@keyframes answerPulse {
  0%, 100% { text-shadow: 0 0 30px var(--glow-gold); }
  50% { text-shadow: 0 0 50px var(--glow-gold), 0 0 80px var(--glow-gold); }
}
```

### 5. Timer Component
```
┌─────────────────────────────────────────────────────────┐
│  TIMER                                           0:24   │
│  [████████████████░░░░░░░░░░░░░░░]                     │
│                                                         │
│  [▶ START]    [⏸ PAUSE]    [↺ RESET]    [⚙ 30s ▼]     │
└─────────────────────────────────────────────────────────┘
```

**Styling:**
```css
.timer-bar {
  height: 8px;
  background: var(--bg-elevated);
  border-radius: 4px;
  overflow: hidden;
}

.timer-progress {
  height: 100%;
  background: var(--timer-safe);
  transition: width 1s linear, background 0.3s ease;
  border-radius: 4px;
}

.timer-progress.warning { background: var(--timer-warning); }
.timer-progress.danger {
  background: var(--timer-danger);
  animation: timerPulse 0.5s ease-in-out infinite;
}

@keyframes timerPulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.6; }
}

.timer-display {
  font-family: var(--font-mono);
  font-size: 2rem;
  font-weight: 600;
}
```

### 6. Action Buttons
```
┌────────────────────┐  ┌────────────────────┐
│   🎴 AUFDECKEN     │  │   ➡️ NÄCHSTE FRAGE  │
└────────────────────┘  └────────────────────┘
```

**Primary Button (Aufdecken):**
```css
.btn-primary {
  background: var(--gradient-gold);
  color: #000;
  font-weight: 700;
  font-size: 1.125rem;
  padding: 1rem 2rem;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 4px 20px var(--glow-gold);
}

.btn-primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 30px var(--glow-gold);
}

.btn-primary:active {
  transform: translateY(0);
}
```

**Secondary Button (Nächste Frage):**
```css
.btn-secondary {
  background: transparent;
  color: var(--text-primary);
  font-weight: 600;
  padding: 1rem 2rem;
  border: 1px solid var(--border-default);
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.btn-secondary:hover {
  background: var(--bg-elevated);
  border-color: var(--text-secondary);
}
```

---

## Layout Structure

### Desktop (min-width: 768px)
```
┌─────────────────────────────────────────────────────────────────┐
│                           HEADER                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│     ┌───────────────────────────────────────────────────┐       │
│     │                                                   │       │
│     │               QUESTION CARD                       │       │
│     │                                                   │       │
│     └───────────────────────────────────────────────────┘       │
│                                                                  │
│     ┌────────────────────┐  ┌────────────────────┐              │
│     │     HINWEIS 1      │  │     HINWEIS 2      │              │
│     └────────────────────┘  └────────────────────┘              │
│                                                                  │
│     ┌───────────────────────────────────────────────────┐       │
│     │                    ANTWORT                        │       │
│     └───────────────────────────────────────────────────┘       │
│                                                                  │
│     ┌───────────────────────────────────────────────────┐       │
│     │                     TIMER                         │       │
│     └───────────────────────────────────────────────────┘       │
│                                                                  │
│            [AUFDECKEN]           [NÄCHSTE FRAGE]                │
│                                                                  │
│                        Frage 3 von 51                           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Mobile (max-width: 767px)
```
┌─────────────────────────┐
│        HEADER           │
├─────────────────────────┤
│                         │
│   ┌─────────────────┐   │
│   │    QUESTION     │   │
│   └─────────────────┘   │
│                         │
│   ┌─────────────────┐   │
│   │   HINWEIS 1     │   │
│   └─────────────────┘   │
│                         │
│   ┌─────────────────┐   │
│   │   HINWEIS 2     │   │
│   └─────────────────┘   │
│                         │
│   ┌─────────────────┐   │
│   │    ANTWORT      │   │
│   └─────────────────┘   │
│                         │
│   ┌─────────────────┐   │
│   │     TIMER       │   │
│   └─────────────────┘   │
│                         │
│   [AUFDECKEN]           │
│   [NÄCHSTE FRAGE]       │
│                         │
│    Frage 3 von 51       │
└─────────────────────────┘
```

---

## Animations & Micro-interactions

### 1. Page Load
- Fade in from black (0.5s)
- Title slides down with slight bounce
- Cards stagger in from bottom (0.1s delay each)

### 2. Card Reveal (Hint/Answer)
```css
@keyframes revealContent {
  0% {
    filter: blur(10px);
    opacity: 0;
    transform: scale(0.95);
  }
  100% {
    filter: blur(0);
    opacity: 1;
    transform: scale(1);
  }
}
```

### 3. New Question Transition
- Current question slides out left
- New question slides in from right
- Quick fade for smooth transition

### 4. Button Interactions
- Hover: slight lift (translateY -2px)
- Click: quick press down
- Disabled: reduced opacity, no pointer events

### 5. Timer Animations
- Progress bar smoothly decreases
- Color transition at thresholds
- Pulse effect when critical

---

## Sound Design

### Sound Events
| Event | Sound | Description |
|-------|-------|-------------|
| Hint Reveal | Card flip | Soft "whoosh" + card snap |
| Answer Reveal | Fanfare | Triumphant reveal sting |
| Timer Start | Click | Subtle button click |
| Timer Warning | Tick | Accelerating tick-tock |
| Timer End | Buzzer | Attention-grabbing but not harsh |
| New Question | Shuffle | Cards being shuffled |
| Button Click | Click | Satisfying tactile feedback |

### Implementation
Using Web Audio API with base64-encoded sounds (no external files):
```javascript
const sounds = {
  cardFlip: new Audio('data:audio/mp3;base64,...'),
  reveal: new Audio('data:audio/mp3;base64,...'),
  tick: new Audio('data:audio/mp3;base64,...'),
  buzzer: new Audio('data:audio/mp3;base64,...')
};
```

---

## State Machine

### Question States
```
INITIAL (question shown, all hidden)
    │
    ▼ [AUFDECKEN clicked]
HINT_1_REVEALED
    │
    ▼ [AUFDECKEN clicked]
HINT_2_REVEALED
    │
    ▼ [AUFDECKEN clicked]
ANSWER_REVEALED
    │
    ▼ [NÄCHSTE FRAGE clicked]
INITIAL (new question)
```

### Timer States
```
STOPPED (initial)
    │
    ▼ [START clicked]
RUNNING ◄─────┐
    │         │
    ▼         │ [START clicked]
PAUSED ───────┘
    │
    ▼ [time = 0]
FINISHED (triggers buzzer)
```

---

## Data Structure

```javascript
const questions = [
  {
    id: 100,
    question: "Wie viele Minuten dauert ein Fußballspiel ohne Nachspielzeit?",
    hint1: "Es besteht aus zwei Halbzeiten.",
    hint2: "Jede Halbzeit hat 45 Minuten.",
    answer: "90"
  },
  // ... 50 more questions
];

// Game State
const gameState = {
  currentQuestion: null,
  revealStage: 0,  // 0=none, 1=hint1, 2=hint2, 3=answer
  usedQuestions: [],
  soundEnabled: true,
  timerDuration: 30,
  timerRemaining: 30,
  timerRunning: false
};
```

---

## File Structure

```
/Users/Johannes/Desktop/Hackathon/
├── index.html          # Complete game (single file)
├── game-plan.md        # This document
├── questions.md        # Source question data
└── Spielanleitung.pdf  # Original rules
```

---

## Implementation Checklist

### Phase 1: HTML Structure
- [ ] Create semantic HTML layout
- [ ] Add all UI elements (cards, buttons, timer)
- [ ] Include meta tags for mobile optimization
- [ ] Add Inter font from Google Fonts (or system fallback)

### Phase 2: CSS Styling
- [ ] Implement color system as CSS variables
- [ ] Style all components (cards, buttons, timer)
- [ ] Add responsive breakpoints
- [ ] Implement all animations/transitions
- [ ] Add poker decorative elements

### Phase 3: JavaScript Logic
- [ ] Embed all 51 questions as data array
- [ ] Implement reveal state machine
- [ ] Add random question selection (no repeats)
- [ ] Build timer functionality
- [ ] Add sound system with Web Audio API

### Phase 4: Polish
- [ ] Test all interactions
- [ ] Verify mobile responsiveness
- [ ] Check sound timing
- [ ] Optimize performance

---

## Verification Plan

1. **Visual Check**: Open in browser, verify dark theme and poker styling
2. **Reveal Flow**: Click through all 4 stages (question → hint1 → hint2 → answer)
3. **Question Cycling**: Load 10+ questions, verify no repeats
4. **Timer Test**: Start timer, verify color changes at 50%/25%, verify end sound
5. **Sound Test**: Toggle sound on/off, verify all sound events
6. **Mobile Test**: Open on phone, verify touch interactions and readability
7. **Edge Cases**: Test what happens when all 51 questions used

---

## Success Criteria

The game is complete when:
- A player can pick up their phone at the poker table
- Open the website with one tap
- See a clear question
- Tap to reveal hints one by one (with satisfying feedback)
- Tap to reveal the answer (with dramatic effect)
- Use the timer for betting rounds
- Move to the next question seamlessly
- Play through all 51 questions without repetition
- Everything works offline after first load
