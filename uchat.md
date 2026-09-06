================================================================================
UCHAT V1.0 - SYSTEM SPECIFICATION & ARCHITECTURE PROMPT FOR AI ORCHESTRATOR
================================================================================
PROJECT: uChat V1.0
ECOSYSTEM: usafe.in (Unified with uSafe SSO & uLocate)
TARGET TIMELINE: 2026 / 2027-Ready Architecture, 2030-Grade Cryptographic Defense
CORE PRINCIPLE: Ultra-lightweight stateless relay server (<15MB RSS, $5-10/mo VPS per 100k conns),
                Local-First Edge Computing, Zero-Knowledge Storage, WhatsApp-Clean UX.

--------------------------------------------------------------------------------
1. CORE ARCHITECTURAL SPECIFICATIONS
--------------------------------------------------------------------------------
- Architecture: Local-First with blind cryptographic relay.
- Client Storage: Local SQLite with WAL mode + CRDTs (Yjs/Automerge) for zero-latency offline-first state.
- Web Client: Dedicated PWA utilizing SQLite WASM + Origin Private File System (OPFS).
- Cryptography:
  * Hybrid Post-Quantum Key Exchange (PQXDH: ML-KEM-768 layered over Curve25519).
  * Group Chat Encryption: RFC 9420 MLS (Messaging Layer Security) with O(log N) key ratcheting.
  * Sealed Senders: Zero metadata leakage on sender identity or contact graph.
- Relay Server Footprint:
  * Written in Rust (Tokio + Axum).
  * Stateless sealed-envelope packet router; no message history stored.
  * Deserialization: FlatBuffers / Protobuf (zero-copy memory management).
  * Offline Message Queue: Ephemeral embedded RocksDB/SQLite ring buffer with strict TTL purge upon delivery.
  * Encrypted Backups: Capped at <= 20 MB per user (account state, contact maps, text histories only; excludes blobs/media). Uploaded client-side encrypted via signed URLs directly to S3/R2 object storage.

--------------------------------------------------------------------------------
2. MULTI-PROTOCOL UNIFIED INBOX (CORE SELLING POINT)
--------------------------------------------------------------------------------
uChat aggregates cross-platform conversations into a single, clean WhatsApp-style feed:
- Platforms Bridged: WhatsApp (Web Linked Device protocol), Signal (libsignal client bindings), Telegram (TDLib/MTProto), Gmail (Google Workspace API), uChat Native (PQ-E2EE).
- Visual Provenance:
  * Home Chat List: Origin badge on contact avatar + low-opacity vector watermark next to timestamp.
  * Contextual Theming: Dynamically tints chat bubbles and accents upon opening (WhatsApp green, Signal ultramarine, Telegram ice blue, uChat deep indigo).
  * Smart Identity Merging: View chats as separate platform rows or combined under a unified contact profile with a quick platform-switcher pill.
- Unified Routing: Outgoing replies default to the incoming platform, with manual override switcher in the composer.

--------------------------------------------------------------------------------
3. GMAIL AS A CHAT STREAM
--------------------------------------------------------------------------------
- Emails appear as conversational chat threads; sender name = contact, subject = message preview.
- 3D Rotating Header Card:
  * Tapping sender name initiates a 180-degree flip displaying verified email address, DKIM/SPF status, and subscription metadata.
  * Inline 2-line AI summary rendered above snippet.
  * Action Buttons: [View Full Mail (Modal)], [AI Auto-Reply], [Manual Reply].
- Automatic OTP Extraction: When an email contains an OTP/verification code, body text is suppressed and replaced by a prominent [Copy OTP] button with a 60-second auto-clear clipboard timer.

--------------------------------------------------------------------------------
4. ZERO-LATENCY PRE-DECODED QR ENGINE
--------------------------------------------------------------------------------
- Ingestion Pipeline: Incoming images are scanned asynchronously on local background worker threads (Google ML Kit / ZXing-C++ WASM) before UI rendering.
- Contextual Actions on Long-Press / Mini-Badge Tap:
  * UPI Payment QR: Schema `upi://pay?...` -> Direct [Send Payment] intent invoking GPay, PhonePe, Paytm, BHIM + Copy VPA / Forward.
  * URL QR: Schema `https://...` -> Sandboxed security check -> [Open in Browser] + Copy Link / Forward.
  * Wi-Fi QR: Direct OS Wi-Fi provisioning.

--------------------------------------------------------------------------------
5. AI ARCHITECTURE & COOPERATIVE EDGE-COMPUTE MESH
--------------------------------------------------------------------------------
- Admin Panel AI (OpenClaw):
  * Autonomous agent engine for platform monitoring, spam threshold enforcement, database maintenance, and automated diagnostics via sandboxed SKILL.md scripts.
- Client-Side Quantized SLMs (INT4 via ONNX / ExecuTorch):
  * Medium-End Devices (4-6 GB RAM): MobileLLM-125M / SmolLM-135M (~70 MB RAM) for smart replies, intent parsing, grammar assist.
  * High-End Devices (8-12+ GB RAM): Meta MobileLLM-350M (~190 MB RAM) for long thread summarization, voice-note transcription, complex search.
- P2P Resource Sharing Compute Mesh:
  * Low-end/budget devices offload AI inference to idle peer high-end devices over encrypted WebRTC DataChannels.
  * Host Sharing Eligibility Guardrails: Must be AC charging, unmetered Wi-Fi, screen locked (idle), and battery temp <= 38°C.
  * Blinded Inference: User identity stripped, prompt encrypted with ephemeral session key, run in WASM/TEE sandbox, all tokens purged immediately upon dispatch.
- In-Chat Assistant (Aura):
  * Action-Detection: Highlights actionable text (e.g., "Meet at Book Club at 9pm") with one-tap chips: [Set Alarm], [Add to Calendar], [Add To-Do].
  * Context Forwarding: Forward any message from any network to the assistant with prompt instructions.

--------------------------------------------------------------------------------
6. DURESS SOS & PERSONAL DEFENSE SYSTEM
--------------------------------------------------------------------------------
- Continuous SOS Telemetry:
  * GPS coordinates dispatched every 30 seconds to pre-configured emergency contacts.
  * Front and rear camera snapshots captured silently on device unlock.
  * Ambient background audio recorded and uploaded in 15-minute encrypted chunks.
- Duress SOS PIN (Decoy Mode):
  * Entering the emergency Duress PIN unlocks the app without error into a realistic Decoy State.
  * Decoy State preserves real contact names but populates conversations with plausible, AI-generated synthetic mundane chats.
  * Triggers silent background SOS telemetry (GPS, camera, audio) with zero UI alerts.
  * Lockdown Exit: Requires both Master PIN and an out-of-band OTP from emergency contacts.

--------------------------------------------------------------------------------
7. INTEGRATION DEPENDENCIES
--------------------------------------------------------------------------------
- SSO Provider: uSafe SSO (account.usafe.in) -> Passkey/WebAuthn FIDO2, OAuth 2.1 PKCE, Ed25519 JWTs.
- Telemetry: uLocate v1.0 -> Kalman-filter sensor fusion dead-reckoning (Rust/WASM) with homomorphic geofencing.
- Management Portal: uSafe Accounts v1.0 over Tailscale mesh overlay.
================================================================================
