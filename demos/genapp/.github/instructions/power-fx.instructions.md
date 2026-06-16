---
description: "Use when writing, reviewing, or debugging Power Fx formulas in Canvas Apps. Covers delegation rules, offline patterns, variable naming, error handling with IfError, and Patch syntax for Dataverse."
---
# Power Fx Guidelines — VisitTrack Canvas Apps

## Variable Naming
- Global variables: `gbl` prefix — `gblCurrentUser`, `gblSelectedVisit`
- Local variables: `loc` prefix — `locLoading`, `locError`
- Collections: `col` prefix — `colVisits`, `colOfflineVisits`

## Control Naming
- Buttons: `btnSaveVisit`, `btnNewPhysician`
- Labels: `lblPhysicianName`, `lblVisitDate`
- Galleries: `galVisits`, `galPhysicians`
- Forms: `frmVisitDetail`
- Text inputs: `txtSearch`, `txtNotes`
- Date pickers: `datVisitDate`
- Dropdowns: `drpPhysician`

## Delegation Rules (Dataverse)
Delegable (no 2000-record limit):
- `Filter` with `=`, `<>`, `<`, `>`, `StartsWith` on indexed columns
- `SortByColumns`
- `Search` (text search on searchable columns)

NOT delegable (only first 500/2000 records processed locally):
- `Contains`, `EndsWith`
- `Mid`, `Left`, `Right` in filters
- Complex nested conditions beyond two levels

**Rule:** Always use `StartsWith` for name search, never `Contains`.

## Offline Pattern
```powerfx
// Always check connection before Dataverse calls
If(
    Connection.Connected,
    /* online path */,
    /* offline fallback using local collection */
)
```

Show/hide online-only controls:
```powerfx
// Visible property of online-only controls
Connection.Connected
```

## Error Handling
Always wrap `Patch()` and `Remove()` in `IfError()`:
```powerfx
IfError(
    Patch(vt_visits, Defaults(vt_visits), { ... }),
    Notify("Fehler: " & FirstError.Message, NotificationType.Error),
    Navigate(VisitListScreen, ScreenTransition.Slide)
)
```

## Validation Pattern
```powerfx
If(
    IsBlank(drpPhysician.Selected),
    Notify("Arzt ist Pflichtfeld.", NotificationType.Error),
    If(
        datVisitDate.SelectedDate > Today(),
        Notify("Datum darf nicht in der Zukunft liegen.", NotificationType.Error),
        /* proceed */
    )
)
```

## Async Loading Pattern
```powerfx
// OnVisible of screen
Set(locLoading, true);
ClearCollect(colVisits, Filter(vt_visits, vt_owner = User().Email));
Set(locLoading, false)
```
