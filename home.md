# 🃏 poker — home

## 📍 where we are
![[status#phase]]
![[status#current focus]]

## 👀 waiting on me
![[awaiting_owner_review#decisions to accept or amend]]
![[awaiting_owner_review#edits to confirm]]
![[awaiting_owner_review#questions to answer]]

## 🚧 blocked initiatives
```dataview
TABLE priority, target
FROM "02_initiatives/ongoing"
WHERE status = "blocked"
SORT priority ASC
```

## 🚀 active initiatives
```dataview
TABLE priority, milestone, target
FROM "02_initiatives/ongoing"
WHERE status = "active"
SORT priority ASC
```

## 💡 proposed initiatives
```dataview
TABLE priority, milestone
FROM "02_initiatives/ongoing"
WHERE status = "proposed"
SORT priority ASC
```

## ⚖️ decisions waiting on me
```dataview
TABLE date, initiative
FROM "03_decisions"
WHERE status = "proposed"
SORT date ASC
```

## 📥 inbox (needs triage)
```dataview
LIST
FROM "00_inbox"
WHERE type = "inbox"
SORT file.ctime DESC
```

## ✅ open tasks

### 🚀 by initiative
```dataview
TASK
FROM "02_initiatives/ongoing"
WHERE !completed
GROUP BY file.link
```

### 🗃️ backlog
```dataview
TASK
WHERE file.name = "backlog" AND !completed
```

### 📆 from daily notes
```dataview
TASK
FROM "01_daily"
WHERE !completed
GROUP BY file.link
```

## 🧾 recent decisions
```dataview
TABLE status, date
FROM "03_decisions"
SORT file.name DESC
LIMIT 5
```

## 🗓️ recent daily notes
```dataview
LIST
FROM "01_daily"
WHERE type = "daily"
SORT file.name DESC
LIMIT 7
```

## 🔗 quick links
[[status]] · [[awaiting_owner_review]] · [[vision]] · [[roadmap]] · [[backlog]] · [[glossary]]
