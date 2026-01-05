# Nexus App - Planned Improvements

A curated list of high-priority improvements for the Nexus Attendance Tracker app.

---

## 1. Edit Past Attendance (Calendar View)

**Priority:** High  
**Category:** Feature Enhancement

### Current Behavior
- Clicking dates in the calendar only **views** logged attendance
- Users cannot modify historical data

### Proposed Changes
- Add inline editing capability when a past date is selected
- Display the same Present/Absent/Cancelled buttons as the daily logger
- Auto-save changes and update stats in real-time
- Add visual indicator for edited entries

### Implementation Notes
- Modify `selectDate()` function to enable editing mode
- Reuse status button components from `renderLogger()`
- Add confirmation for bulk changes

---

## 2. Haptic Feedback

**Priority:** Medium  
**Category:** UX Enhancement

### Description
Add tactile feedback on button interactions for a more native mobile feel.

### Proposed Changes
- Add `navigator.vibrate(10)` on button clicks (short pulse)
- Longer vibration (50ms) for important actions like saving/deleting
- Respect user's system haptic preferences

### Implementation Notes
```javascript
function hapticFeedback(duration = 10) {
  if ('vibrate' in navigator) {
    navigator.vibrate(duration);
  }
}
```

Add to:
- Status buttons (Present/Absent/Cancelled)
- Navigation tabs
- Quick log buttons
- Toggle switches
- Modal confirmations

---

## 3. Swipe Gestures

**Priority:** Medium  
**Category:** UX Enhancement

### Description
Enable touch-based navigation for a more intuitive mobile experience.

### Proposed Features
1. **Tab Navigation**: Swipe left/right to switch between main tabs
2. **Calendar Navigation**: Swipe left/right to change months
3. **Syllabus Expansion**: Swipe to expand/collapse subjects

### Implementation Notes
- Use native Touch Events API (no external library needed)
- Track `touchstart` and `touchend` positions
- Minimum swipe threshold: 50px horizontal, max 30px vertical
- Add subtle animation during swipe

```javascript
let touchStartX = 0;
let touchStartY = 0;

document.addEventListener('touchstart', (e) => {
  touchStartX = e.touches[0].clientX;
  touchStartY = e.touches[0].clientY;
});

document.addEventListener('touchend', (e) => {
  const deltaX = e.changedTouches[0].clientX - touchStartX;
  const deltaY = Math.abs(e.changedTouches[0].clientY - touchStartY);
  
  if (Math.abs(deltaX) > 50 && deltaY < 30) {
    if (deltaX > 0) navigatePrev();
    else navigateNext();
  }
});
```

---

## 4. Enhanced Subject-wise Attendance Display

**Priority:** High  
**Category:** Dashboard Enhancement

### Current Behavior
- Basic grid cards with progress bars
- No visual warnings or detailed insights

### Proposed Changes

#### A. Mini Progress Rings
Replace simple progress bars with circular progress indicators:
- Compact SVG rings (40x40px)
- Color-coded based on target threshold
- Animated on load

#### B. Warning System
- **Critical Alert**: If any subject drops below 60%
- **Warning Alert**: If any subject is within 5% of target
- Show "Lowest Subject" badge on the dashboard

#### C. Subject Quick Actions
- Tap subject card to see detailed breakdown
- Show last 7 days trend (mini sparkline)
- Quick link to subject's syllabus section

### Visual Mockup
```
┌─────────────────────────────────┐
│  ⚠️ Warning: ETCG at 73%       │
│     Attend 2 more to be safe    │
└─────────────────────────────────┘

┌─────────┐  ┌─────────┐
│  IDTX   │  │  AUDA   │
│  ◔ 85%  │  │  ◔ 78%  │
│  P:12   │  │  P:10   │
└─────────┘  └─────────┘
```

---

## 5. Code Modularization

**Priority:** High  
**Category:** Technical Debt / Maintainability

### Current State
- Single monolithic `index.html` file (~2230 lines)
- All CSS, JavaScript, and HTML in one file
- Difficult to maintain and extend

### Proposed Architecture

```
/nexus-attendance-tracker
├── index.html              # Main HTML shell
├── manifest.json
├── sw.js
├── css/
│   ├── base.css           # Variables, resets, typography
│   ├── components.css     # Cards, buttons, modals
│   ├── views.css          # Dashboard, Logger, Calendar styles
│   └── syllabus.css       # Syllabus-specific styles
├── js/
│   ├── app.js             # Main initialization
│   ├── config.js          # Constants (SUBJECTS, SCHEDULE, etc.)
│   ├── state.js           # State management, localStorage
│   ├── utils.js           # Helper functions
│   └── views/
│       ├── dashboard.js
│       ├── logger.js
│       ├── calendar.js
│       ├── syllabus.js
│       └── settings.js
└── assets/
    └── icons/
```

### Benefits
- **Maintainability**: Each view has its own file
- **Collaboration**: Easier for multiple developers
- **Testing**: Can unit test individual modules
- **Caching**: CSS/JS files can be cached separately
- **Debugging**: Clearer stack traces

### Migration Strategy
1. Extract CSS into separate files first (lowest risk)
2. Create `config.js` with constants (SUBJECTS, SCHEDULE, etc.)
3. Extract utility functions to `utils.js`
4. Create view modules one at a time
5. Update service worker to cache new files

---

## Implementation Priority

| # | Feature | Effort | Impact | Priority |
|---|---------|--------|--------|----------|
| 1 | Edit Past Attendance | Medium | High | 🔴 P1 |
| 4 | Enhanced Subject Display | Medium | High | 🔴 P1 |
| 5 | Code Modularization | High | High | 🟡 P2 |
| 2 | Haptic Feedback | Low | Medium | 🟢 P3 |
| 3 | Swipe Gestures | Medium | Medium | 🟢 P3 |

---

## Next Steps

1. [ ] Implement Edit Past Attendance feature
2. [ ] Add haptic feedback to all buttons
3. [ ] Implement swipe gestures for navigation
4. [ ] Redesign subject cards with mini rings and warnings
5. [ ] Begin code modularization (start with CSS extraction)

---

*Last Updated: January 5, 2026*
