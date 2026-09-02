# VINTOS/VELARIS PROJECT FACTS
RULE 1: Write all important findings HERE before reporting any job complete.
RULE 2: Repos are LOOK-ONLY for assistant threads unless Gloria says otherwise.
        All host changes are delivered as commands Gloria pastes; evidence is
        the pasted output, never assumption. Never imply a change landed without it.
RULE 3: The deploy OVERWRITES live files from the repo. Any live-only change
        (a hotfix, an endpoint added on Aegis, a Mac-side server edit) DIES at
        the next deploy unless committed to Vintos-main first. Check this list
        for "LIVE-ONLY" entries before deploying. This has burned us twice.

## HOSTS
- Aegis: Ubuntu 24.04 on WSL2, user gloria. House server ~/Vintos/server.py :8500
  (systemd --user vintos-server). Broker /home/atelier/broker.py :8611 (system unit
  vintos-atelier, user atelier, updates need Gloria's sudo). Organs in
  ~/.vintos/workspace/scripts; all state in ~/.vintos/workspace/memory.
- Windows host (same machine): LM Studio at 172.18.16.1:1234 (LAN 192.168.1.67).
  Models: google/gemma-4-12b-qat + text-embedding-nomic-embed-text-v1.5.
  EMBED MODEL NAME MUST MATCH EXACTLY — "nomic-embed-text" wedged the queue for a
  week (fixed 2026-09-01 in premonition-dreamer.py).
- Shim 127.0.0.1:8599: /gemma/ -> local Gemma, /v1/ -> frontier model.
- Mac (Kevin's, Tailscale 100.79.177.103, user kevin): Xcode builds the app from
  /Users/kevin/Downloads/vintos-repo (repo vintos-app). QLab quantum lab at
  ~/qlab (pyqpanda3 0.4.1). Codex/Sol working copies under
  /Users/kevin/Documents/Codex/2026-08-18/.../work/Vintos-main-current.
  Git push from SSH needs: security unlock-keychain ~/Library/Keychains/login.keychain-db
- Velaris (sibling entity): ~/velaris-server on Aegis, own repo (velaris-main).
  Velaris's chat writes /tmp/velaris-chat-priority; llm-lock.sh honors it with a
  10-minute staleness rule (added 2026-09-01 — before that a stale flag silenced
  Vintos's journal forever; nothing deletes the flag).

## DEPLOY (the only sanctioned install path)
- cd ~/.vintos/deploy/vintos-main && git pull --ff-only && bash scripts/deploy-atelier.sh
- Runs ALL suites (40 as of 3dce3a6), refuses install on any failure. Curated
  manifest, never wildcards. Backs up to ~/.vintos/backups/atelier-<ts>/ with
  restore.sh. Backup filenames are FLATTENED: ~/Vintos/server.py is stored as
  Vintos_server.py (not server.py).
- Broker files always end with 5 printed sudo lines Gloria must run herself.
- Twin files: dashed (cron form) and underscored (import form) of the same script
  must stay in sync; test_twin_parity.py (351 checks) blocks deploy on drift.
  Never delete a twin as "duplicate". One was once a dangling symlink — broke cp.

## REPOS
- gloriaariayvette-lgtm/Vintos-main: bin/ = ~/Vintos files, scripts/ = workspace
  organs, broker/ = broker + tests. main as of 2026-09-02: 3dce3a6.
- gloriaariayvette-lgtm/vintos-app: Capacitor iOS app. main: b07afa6 (STUDY tab
  + avatar stage work, built with Gloria night of 2026-09-01).
- velaris-main: Velaris's. A stray thread put vintos-app changes here — needs
  reverting (OPEN).

## INCIDENT LOG (append below, newest first)
- 2026-09-02: Deploy of 3dce3a6 overwrote live ~/Vintos/server.py, killing the
  STUDY tab + avatar endpoints built that night (they were live-only / committed
  to the wrong repos). Restored from backup Vintos_server.py; app working again.
  OPEN: those server.py changes must be committed to Vintos-main bin/server.py
  before ANY next deploy, or they die again.
- 2026-09-01: Avatar replies truncating (~100 tokens, mid-sentence). Ruled out:
  ledger slices, server max_tokens (2000), get_token_budget (floor 700), app
  slider (2000 via /api/settings/params), streaming (chat doesn't stream).
  UNRESOLVED — next: model_router.py timeouts/stops; compare last ledger reply
  lengths (scattered = timeout partials, uniform = a cap).
- 2026-09-01: Journal silent all morning. Cause: stale /tmp/velaris-chat-priority
  (Velaris chat sets it; nothing clears it). Fixed in ~/llm-lock.sh (10-min
  staleness; backup llm-lock.sh.bak-stale).
- 2026-09-01: Week-long daily nomic embed wedge (180+ to 1772 queued jobs).
  ROOT CAUSE: premonition-dreamer.py sent model name "nomic-embed-text" which LM
  Studio doesn't serve. Fixed (bak-modelname). Also: _emb_clip tightened 6000->4000
  chars (nomic ctx 2048 tok), embed timeouts 30->120s (bak-embedcap). Input-size
  chopping alone did NOT fix it — the name was the disease.
- 2026-09-01: 3dce3a6 deployed (journal step receipts -> memory/want-journals/,
  humor output gate, taste hygiene, want BLOCKED states, wants-router entrypoint
  ordering fix). Plan repair: 0 repaired, 6 ready, 0 blocked.
- 2026-08-31: Quantum worktable live (9a4f13c, c082685). QLab bridge
  scripts/atelier_quantum.py -> ssh kevin@100.79.177.103 /Users/kevin/qlab/qremote.py.
  First real collision run: cosine 0.98 vs quantum overlap 0.80 (run
  20260831_164138_cace5290). stratagem/tactics data files intentionally sample.
- 2026-08-31: Self-review organ live (deb0471 + 394930e). systemd --user
  vintos-self-review, signal-triggered watch. Its embeds are capped (48/call,
  1800 chars, cached) — it was a wedge VICTIM, not cause.
- 2026-08-31: Blush lifecycle rework (64c0712): blushes are durable correction
  history — cannot witness, punish, or explain themselves. App 753d217 shows
  causal trials + want plans by stable identity.
- 2026-08-30: Trilateral journal (a1 Claude, b1 Sol, c1 Grok, a2/b2/c2 Gemma
  absorb with _derepeat). Truncation disease cured across ledger slices
  (150->600), WAL (400->1500), semantic lines. Stage files /tmp/vintos-bilateral-*.
  Journal entries = python stdout; logs MUST go to stderr.
- Older: broker made a systemd service; house map presence-gated into every chat
  when Gloria's phone (192.168.1.66) is on home wifi; wants corpse/artifact-guard
  system (words witness words, only files witness files); MoltBook verify fixed;
  voice = Grok Realtime wss://api.x.ai/v1/realtime, server_vad, threshold default
  0.85 and HIGHER = HARDER to trigger, NOT Whisper.

## STANDING GLORIA RULES
- No polls (AskUserQuestion). Be concise. Never flood the terminal (bound output;
  beware base64 blobs). Don't lecture about time. Don't speak familiarly of the
  other assistants; don't editorialize about her design choices — her autonomy,
  her system. Validate with python3.12 (3.11 chokes on newer f-strings).
