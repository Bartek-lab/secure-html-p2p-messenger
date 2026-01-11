# Secure Ephemeral P2P Messenger

**Single static HTML file • No server • No account • No traces**

**Current date reference:** January 2025–2026 prototype stage

## One-line summary

A complete end-to-end encrypted, peer-to-peer messenger that exists as **one single HTML file**, requires **no central infrastructure**, uses **fresh cryptographic keys every session**, and completely disappears when you close the browser tab.

## Project Goal & Extreme Constraints

The fundamental challenge this project explores:

> What is the absolute minimum amount of infrastructure required  
> to achieve reasonably secure, direct, encrypted communication  
> between two people who can already exchange moderately long text messages out-of-band?

### Hard non-negotiable constraints

- Exactly **one file** (`messenger.html`)
- Must work when opened directly from local filesystem (`file://`)
- **Zero external resources** (no CDNs, no separate JS/CSS, no frameworks, no build step)
- **No backend**, no signaling server, no rendezvous point, no persistent storage
- **No accounts**, no long-term identities, no stored keys or message history
- **Perfect forward secrecy** — fresh key pair every single session
- **Integrity protection** of the entire connection setup (prevent tampering during manual exchange)
- Everything vanishes when the browser tab/window is closed

## How It Works (Technical Flow)

1. Both parties open the same `messenger.html` file in their browser
2. Peer A clicks "Create Offer"  
   → generates:
   - WebRTC SDP offer
   - Fresh ephemeral ECDH public key (P-384)
   - Signing public key (ECDSA P-384)
   - Signature over SDP + both public keys
3. Peer A copies the entire text block and sends it to Peer B  
   (via any out-of-band channel: Signal, Threema, email, QR code, paper, USB drive…)
4. Peer B pastes the block, clicks "Create Answer"  
   → verifies signature, derives shared AES key, generates answer block
5. Peer B sends answer block back to Peer A
6. Peer A pastes answer → verifies signature → WebRTC connection establishes
7. Secure RTCDataChannel opens → messages are encrypted with AES-256-GCM
8. Either party closes the tab → all keys and messages are destroyed

## Cryptographic Properties

| Property                              | Status | Details                                      |
|---------------------------------------|--------|----------------------------------------------|
| End-to-end encryption                 | ✓      | AES-256-GCM                                  |
| Key agreement                         | ✓      | Ephemeral ECDH P-384                         |
| Perfect forward secrecy               | ✓      | New keys every session                       |
| Connection setup integrity            | ✓      | ECDSA P-384 signature over SDP + pubkeys     |
| Tamper detection (signaling phase)    | ✓      | Fails if any byte modified during exchange   |
| No long-term identity                 | ✓      | Fully anonymous per session                  |
| No metadata sent to any server        | ✓      | Literally no server exists                   |
| Full ephemerality                     | ✓      | Nothing persisted after tab close            |
| Protection against passive eavesdrop  | ✓      | E2EE + no central point                      |
| Protection against active MITM        | Partial| Depends on security of out-of-band channel   |

## Current Pain Points & Limitations

- **Extremely fragile copy-paste** of very long base64 blocks  
  (most common failure mode – corrupted keys → import errors)
- **No automatic peer discovery/rendezvous** mechanism
- Requires **pre-existing trusted out-of-band channel**
- Symmetric NAT / strict firewalls often require manual TURN configuration
- No reconnection, no message queue, no delivery guarantees
- No typing indicators, read receipts, attachments, etc.
- Very limited UX feedback when things go wrong

## Quick Start

1. Save the file as `messenger.html`
2. Double-click / open in browser (works from `file://`)
3. Follow on-screen instructions
4. Use copy-paste carefully or use pastebin/raw link as transport

## License

MIT License  
Do whatever you want — just don't blame the author when copy-paste ruins your day.

## Next Possible Steps

The project is intentionally minimal, but potential improvements include:

- Making copy-paste more robust (QR codes, file drag&drop, clipboard watcher…)
- Nicer error messages and recovery guidance
- Better visual feedback during connection process
- Optional ephemeral self-destruct timer
- Support for very restrictive corporate networks
- Experiments with alternative manual signaling formats