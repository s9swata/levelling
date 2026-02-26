# Levelling — Build Prompt
> Real life is an RPG. Every habit you complete earns XP, levels you up, and unlocks new abilities. You are the character.

---

## Tech Stack

- **Frontend:** React Native (Expo) for iOS + Android
- **Backend:** Node.js + Express OR Supabase (BaaS)
- **Database:** PostgreSQL (via Supabase or Prisma ORM)
- **Auth:** Supabase Auth or Clerk
- **AI:** Groq SDK (free tier) — `llama-3.3-70b-versatile`
- **Notifications:** Expo Push Notifications
- **State Management:** Zustand
- **Styling:** NativeWind (Tailwind for React Native)
- **Animations:** React Native Reanimated + Lottie

---

## Core Game Mechanics

### XP & Levelling System
- Every habit completion earns XP (base 50 XP)
- Streak multipliers: 3-day = 1.5x, 7-day = 2x, 30-day = 3x
- Difficulty modifier: Easy = 0.8x, Medium = 1x, Hard = 1.5x, Legendary = 2x
- Level thresholds: Level 1→2 = 500 XP, each level requires 20% more XP than the last
- Show animated XP bar filling up after every completion
- Level up triggers full-screen celebration animation + unlock notification

### Character System
Users create a character on onboarding. The character has **6 STATS** that grow based on habit categories:

| Stat | Category |
|------|----------|
| 💪 Strength | Fitness habits |
| 🧠 Intelligence | Learning / reading habits |
| 🧘 Discipline | Consistency / mindfulness habits |
| ❤️ Vitality | Health / sleep / nutrition habits |
| 💰 Wealth | Finance / career habits |
| 🎨 Creativity | Creative / art habits |

Stats are displayed as a **hexagonal radar chart** on the profile screen.

### Titles & Ranks

| Level Range | Title |
|-------------|-------|
| 1–5 | Novice |
| 6–10 | Apprentice |
| 11–20 | Journeyman |
| 21–35 | Expert |
| 36–50 | Master |
| 51+ | Legend |

Special stat titles: `"Iron Will"` (100 Discipline), `"Scholar"` (100 Intelligence)

### Achievements / Badges
- 🩸 **First Blood** — complete your first habit
- 🔥 **On a Roll** — 7-day streak on any habit
- ⚡ **Unstoppable** — 30-day streak
- 🌍 **Renaissance Human** — all 6 stats above 50
- 🦉 **Night Owl** / 🌅 **Early Bird** — complete habits after 10pm / before 7am
- 👊 **Boss Fight** — complete every habit for 7 days straight

### Daily Quests
- Every day, 3 auto-generated quests are created from the user's habits
- Example: *"Complete your morning run AND meditate today for bonus 200 XP"*
- Quests reset at midnight
- **Weekly Boss Quest:** harder challenge, big XP reward

---

## Features

### 1. Onboarding / Character Creation
- "Your real life adventure begins here"
- Choose pixel art avatar (8 options)
- Name your character
- Pick 3 life areas to improve (maps to the 6 stats)
- Choose 2–3 starter habits
- Show character card at end of onboarding (RPG stat screen style)

### 2. Habit Creation
- Name, description, emoji icon, difficulty (Easy / Medium / Hard / Legendary)
- Category (maps to one of the 6 stats)
- Frequency: daily, specific days, X times per week
- Optional: target completion count per session (e.g. "Read 20 pages")
- XP preview: *"Completing this earns 75 XP + 1 Intelligence"*

### 3. Home / Quest Board Screen
- Title: **"Today's Quests"** — frame daily habits as quests
- Each habit shown as a quest card: icon, name, XP reward, difficulty badge
- Tap to complete → XP animation flies into the XP bar
- Daily quests section at top (bonus XP)
- Progress counter: "X of Y quests complete"
- Overall XP bar + current level visible at top

### 4. Character Profile Screen
- Full character card: avatar, name, title, level, total XP
- Hexagonal radar chart of all 6 stats
- Achievement badges grid
- Current streak stats
- "Power Level" score (sum of all stats)

### 5. Stats & History
- Habit completion heatmap (GitHub-style)
- XP earned per day/week chart
- Stat growth over time (line chart per stat)
- Best streaks, completion rates

### 6. AI Companion — The Oracle (Groq-powered)
- Wise, slightly dramatic AI companion
- Powered by Groq SDK using `llama-3.3-70b-versatile` (free)
- Weekly performance review with XP + stat breakdown
- Daily motivational message based on current streaks and level
- Ask The Oracle anything: coaching, advice, quest suggestions
- Detects weak stats and suggests new habits to balance them
- Gently roasts you if you're slacking: *"Your Vitality hasn't moved in 5 days, hero."*

### 7. Leaderboard & Guilds
- Global leaderboard by total XP and level
- Create or join a Guild (up to 20 members)
- Guild XP: everyone's completions contribute to a shared guild level
- Guild challenges: collective goals with shared rewards

### 8. Streak & Recovery System
- Streak shown prominently (🔥)
- Streak Freeze: 1 free per month
- Recovery Quest: miss a day? Complete a harder version tomorrow for partial streak save
- Streak Shields: earned from achievements, protect against 1 missed day

### 9. Settings
- Dark mode (default) / Light mode
- Notification preferences + custom reminder messages
- Export data (CSV)
- Reset character (with scary confirmation)

---

## Design Language

- **Dark-first UI** — deep navy/dark purple backgrounds (`#0D0D1A`, `#1A1A2E`)
- **Neon accents:** electric indigo `#6C63FF`, gold `#FFD700`, cyan `#00F5FF`
- Pixel/RPG-inspired elements mixed with clean modern UI
- **Typography:** Inter for UI, Press Start 2P for level/XP numbers
- Cards with glowing borders on high-streak habits
- Particle effects / confetti on level up
- Optional: satisfying "ding" sound on habit completion
- Empty state: character looking bored — *"No quests yet, hero. Add your first habit."*

---

## Database Schema (PostgreSQL)

```sql
users (id, email, created_at)

characters (
  id, user_id, name, avatar_id, level, total_xp, current_xp, title,
  stat_strength, stat_intelligence, stat_discipline,
  stat_vitality, stat_wealth, stat_creativity
)

habits (
  id, user_id, name, icon, category, difficulty,
  frequency, xp_reward, created_at, archived_at
)

habit_logs (id, habit_id, user_id, completed_at, xp_earned, note)

streaks (
  id, habit_id, user_id, current_streak, best_streak,
  last_completed_date, streak_freezes_available
)

achievements (id, name, description, icon, xp_reward, condition_type, condition_value)
user_achievements (id, user_id, achievement_id, unlocked_at)

daily_quests (id, user_id, habit_ids[], bonus_xp, date, completed)

guilds (id, name, creator_id, total_xp, level, created_at)
guild_members (id, guild_id, user_id, joined_at, contribution_xp)

ai_messages (id, user_id, role, content, created_at)
```

---

## Groq AI Setup

```bash
npm install groq-sdk
```

```javascript
import Groq from "groq-sdk";

const groq = new Groq({ apiKey: process.env.GROQ_API_KEY }); // free at console.groq.com

const getOracleResponse = async (userMessage, characterData) => {
  const response = await groq.chat.completions.create({
    model: "llama-3.3-70b-versatile",
    messages: [
      {
        role: "system",
        content: `You are The Oracle — a wise, slightly dramatic AI companion 
in a real-life RPG app called Levelling. The user is a hero on a journey 
to level up in real life through daily habits.

Their character data: ${JSON.stringify(characterData)}

Your personality: wise, encouraging, occasionally dramatic 
("The path ahead is treacherous, but you are ready."), 
uses light RPG/fantasy language naturally.

Your job:
1. Analyze their stats and identify growth areas
2. Give specific, actionable advice — not generic motivation
3. Reference their actual data (level, XP, streaks, weak stats)
4. Keep responses concise: 2-4 sentences for nudges, up to 8 for weekly reviews
5. If they're slacking, call it out with humor and encouragement
6. Suggest specific habits to improve weak stats`,
      },
      { role: "user", content: userMessage },
    ],
    temperature: 0.8,
    max_tokens: 300,
  });

  return response.choices[0].message.content;
};
```

---

## XP & Level Calculation Logic

```javascript
// XP required to reach next level
const xpForLevel = (level) => Math.floor(500 * Math.pow(1.2, level - 1));

// Total XP required to reach a level from level 1
const totalXpForLevel = (level) => {
  let total = 0;
  for (let i = 1; i < level; i++) total += xpForLevel(i);
  return total;
};

// Calculate level from total XP
const getLevelFromXP = (totalXP) => {
  let level = 1;
  while (totalXP >= totalXpForLevel(level + 1)) level++;
  return level;
};

// XP reward per habit completion
const calculateXP = (baseXP, difficulty, streakDays) => {
  const difficultyMultiplier = { easy: 0.8, medium: 1, hard: 1.5, legendary: 2 };
  const streakMultiplier =
    streakDays >= 30 ? 3 : streakDays >= 7 ? 2 : streakDays >= 3 ? 1.5 : 1;
  return Math.floor(baseXP * difficultyMultiplier[difficulty] * streakMultiplier);
};
```

---

## Screens to Build (in order)

1. Splash + Character Creation (Onboarding)
2. Home / Quest Board
3. Add / Edit Habit (Quest)
4. Habit Completion Animation (full-screen XP gain moment)
5. Character Profile + Radar Chart
6. Stats & History
7. The Oracle (AI Chat)
8. Achievements Screen
9. Guild / Social Screen
10. Settings

---

## Build Order

1. Project setup (Expo + Supabase + NativeWind)
2. Auth (sign up / log in)
3. Character creation flow
4. Home Quest Board with habits
5. Habit completion → XP animation → level up logic
6. Character profile with radar chart
7. Layer in: Groq AI, achievements, guilds

> Make it production-ready: proper error handling, loading skeletons, empty states, and offline support for habit logging.
