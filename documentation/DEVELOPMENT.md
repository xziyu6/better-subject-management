# Better Subject Management - Development Notes

## Mod Overview
This mod replaces the blanket liberty desire increase when reducing subject autonomy with a strategic, region-based system.

## Key Mechanics

### Vanilla Behavior (Before Mod)
- Decrease any subject's autonomy → ALL other subjects +10 liberty desire
- Exponentially harder with more subjects

### Modded Behavior (After Mod)
- Decrease any subject's autonomy → Only subjects with regional overlap get +10 liberty desire
- Regional overlap determined by:
  1. Having interest markers in target's regions
  2. Owning states in same strategic regions as target

## Technical Implementation

### 1. Diplomatic Action Override
**File:** `common/diplomatic_actions/bsm_subjects_decrease_autonomy.txt`

Uses `REPLACE:da_decrease_autonomy` to completely replace the vanilla diplomatic action.

**Key Changes:**
```vic3
# OLD (Vanilla):
every_direct_subject = {
    limit = { NOT = { this = scope:target_country } }
    add_liberty_desire = decrease_autonomy_liberty_desire_to_add
}

# NEW (Mod):
every_direct_subject = {
    limit = {
        NOT = { this = scope:target_country }
        OR = {
            # Has interest markers in target's regions
            any_interest_marker = { ... }
            # Owns states in same regions
            any_scope_state = { ... }
        }
    }
    add_liberty_desire = decrease_autonomy_liberty_desire_to_add
}
```

### 2. Scripted Triggers
**File:** `common/scripted_triggers/bsm_subject_triggers.txt`

Contains helper triggers (currently for reference, logic is inline in diplomatic action):
- `bsm_has_overlapping_strategic_interests`
- `bsm_has_shared_strategic_region`

### 3. Localization
**File:** `localization/english/bsm_subjects_l_english.yml`

Custom tooltip text explaining the new behavior.

## Game Concepts Used

### Strategic Regions
- Pre-defined geographic regions (e.g., region_india, region_china)
- Each state belongs to exactly one strategic region
- Found in: `common/strategic_regions/`

### Interest Markers
- Countries place interest markers in strategic regions
- Used for diplomatic interactions and influence
- Accessed via `any_interest_marker` scope

### States
- Basic territorial units
- Each state has a `.region` property linking to strategic region
- Accessed via `any_scope_state`

## Testing Checklist

- [ ] Load game without errors
- [ ] Decrease autonomy of a subject in Region A
- [ ] Verify only subjects with interests/states in Region A get +10 LD
- [ ] Verify subjects in other regions are unaffected
- [ ] Check tooltip displays correct information
- [ ] Test with multiple subjects in same/different regions
- [ ] Verify AI behavior still functions

## Compatibility Notes

### Compatible With:
- Save games (can be added/removed mid-game)
- Other mods that don't modify `da_decrease_autonomy`
- Multiplayer (synchronized)

### Potentially Incompatible With:
- Mods that also REPLACE `da_decrease_autonomy`
- Solution: Merge changes manually or use INJECT if possible

## Future Enhancements

Possible additions:
1. Make the liberty desire value configurable
2. Add different penalties based on relationship levels
3. Consider cultural/religious factors alongside geography
4. Add events explaining the mechanic to players
5. UI indicators showing which subjects will be affected before confirming

## Debugging

### Enable Debug Mode
Add `-debug_mode` to launch options or enable in launcher settings

### Console Commands
```
# Check subject liberty desires
effect scope:target_country = { add_liberty_desire = 10 }

# View interest markers
nudge
```

### Log Files
- Location: `Documents\Paradox Interactive\Victoria 3\logs\error.log`
- Check for: script errors, scope issues, missing localizations

## Victoria 3 Scripting Reference

### Scopes Used
- `root` - The overlord executing the action
- `scope:target_country` - The subject having autonomy reduced
- `scope:checking_marker` - Temporary scope for interest markers
- `scope:subject_region` - Temporary value for region comparison

### Key Triggers
- `any_interest_marker` - Iterate over country's interest markers
- `any_scope_state` - Iterate over country's states
- `any_direct_subject` - Iterate over direct subjects
- `region = <value>` - Compare state region

### Key Effects
- `add_liberty_desire` - Modify subject's liberty desire
- `save_temporary_scope_as` - Create temporary named scope
- `save_temporary_scope_value_as` - Create temporary named value

## Resources

- [Victoria 3 Modding Wiki](https://vic3.paradoxwikis.com/Modding)
- [Effect Documentation](https://vic3.paradoxwikis.com/Effect)
- [Trigger Documentation](https://vic3.paradoxwikis.com/Trigger)
- [Modding Discord](https://discord.gg/XJbqFbHdsM)
