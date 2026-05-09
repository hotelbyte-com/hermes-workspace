# HermesWorld Mantella Dialogue Corpus — Safe Integration Notes

Purpose: additive persona/dialogue corpus for deity and major NPC dialogue while keeping the current overnight lane safe.

Scope shipped here:
- `Assets/HermesWorld/Data/HermesWorldDialoguePersonaCorpus.json`
- `Assets/HermesWorld/Data/HermesWorldDialogueRuntimeSafety.json`

Remote inspection findings from PC1:
- `Assets/HermesWorld/Runtime/HermesWorldDeityDialog.cs` exists.
- `Assets/HermesWorld/Data/DeityPersonaLibrary.asset` exists with six deity seeds.
- `Assets/HermesWorld/Docs/MANTELLA_DEITY_DIALOG_SPEC.md` exists.
- `HermesWorldDialogService.UseMockResponses = true` is already the default in the landed runtime scaffold.
- `ApplyActions(...)` currently logs approved action intents and does not execute live Atavism mutations.

What this corpus adds:
- Six deity profiles aligned to current HermesWorld canon: Hermes, Athena, Apollo, Artemis, Eros, Nike.
- Six major NPC role profiles for non-deity dialogue coverage: Daimon Companion, Scholar Proctor, Sun Arcade Healer, Garden Shrinekeeper, Moon Gate Warden, Caravan Marshal.
- ScriptableObject-friendly JSON structure with fields for persona seeds, voice character, action allowlist, conversation lanes, and canonical lines.
- Explicit runtime safety defaults so future importers/loaders do not accidentally flip into live mode.

Safety lock:
- Default mock mode must remain `true` until a reviewed offline/local-only endpoint path is approved.
- Action intents remain `log_only`.
- No live TTS, no live LLM, no external API keys.
- Any future importer should treat the JSON as content only, never as authority to mutate Atavism state.

Suggested next safe step:
- Add an editor-only importer under `Assets/HermesWorld/Editor/` that reads `HermesWorldDialoguePersonaCorpus.json` and refreshes a local `DeityPersonaLibrary` or NPC ScriptableObject assets without changing runtime behavior.
