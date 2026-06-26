# PS5 FontFace UAF Exploit

**Target:** PS5 Firmware 12.00+  
**Technique:** CSSFontFace UAF → ARW → P2JB Kernel Chain  
**Authors:** ntfargo / ufm42 / DrYenyen (WebKit) + Gezine / cheburek3000 (P2JB)

## ⚠️ Requirements

- **PS5 System Browser** (NOT YouTube app)
- `FontFace` API must be available
- HTTPS hosting (PS5 browser blocks HTTP)

## 🚀 Quick Start

### Method 1: GitHub Pages (Easiest)

1. Fork this repository
2. Go to **Settings → Pages**
3. Select branch `main` / folder `root`
4. Your exploit is live at `https://yourusername.github.io/ps5-fontface-exploit/`
5. Open this URL in the **PS5 System Browser**

### Method 2: DNS Hijack (Recommended)

1. Set PS5 DNS to your server (or use Pi-hole)
2. Point `manuals.playstation.net` → your server IP
3. Open **Settings → User's Guide** on PS5
4. The browser loads this exploit automatically

### Method 3: Direct Browser

https://mounirhero.github.io/PS5-WEBKIT-12.00/


1. Host `index.html` on any HTTPS server
2. Navigate to the URL from PS5 System Browser
3. Press **RUN EXPLOIT**

## 📁 File Structure

```
├── index.html          # Main exploit page (self-contained)
├── README.md           # This file
└── .github/
    └── workflows/
        └── pages.yml   # Auto-deploy to GitHub Pages
```

## 🔧 How It Works

### Stage 0: CSSFontFace UAF
- Creates FontFace A (local source, sync resolve)
- Creates FontFace B via CSS `@font-face` rule
- Calls `document.fonts.load("a, b", "AB")`
- Intercepts `.then` getter on A to free B during load
- Reclaims freed CSSFontFace with ArrayBuffer

### Stage 1: ARW Primitives
- Uses reclaimed ArrayBuffer to corrupt adjacent objects
- Achieves `addrof()` / `fakeobj()` / `read64()` / `write64()`

### Stage 2: P2JB Kernel Chain
- `kqueueex` UAF (cr_ref overflow)
- IPv6 socket options for memory reclaim
- Pipe corruption for fast kernel R/W
- Credential escalation to `SYSTEM_AUTHID`

## 📋 Validated Offsets (FW 12.00)

| Symbol | Offset | Status |
|--------|--------|--------|
| `DATA_BASE_ALLPROC` | `0x02885E00` | ✅ Validated |
| `DATA_BASE_SECURITY_FLAGS` | `0x00D83064` | ✅ Validated |
| `DATA_BASE_KERNEL_PMAP_STORE` | `0x02E1CFB8` | ✅ Validated |
| `DATA_BASE_GVMSPACE` | `0x02E7E570` | ✅ Validated |
| `PROC_UCRED` | `0x40` | ✅ Standard FreeBSD |
| `UCRED_CR_SCEAUTHID` | `0x58` | ✅ Sony auth |

## 🛡️ Troubleshooting

| Issue | Solution |
|-------|----------|
| "FontFace not available" | Use PS5 System Browser, not YouTube app |
| HTTPS error | Use GitHub Pages or ngrok (free HTTPS) |
| "UAF trigger FAILED" | Refresh page and retry (heap randomization) |
| Browser crashes | Reboot PS5 and try again |

## ⚖️ Disclaimer

This is for **security research and homebrew development** on devices you own.  
Do not use for piracy, cheating, or any illegal activity.

## 🙏 Credits

- **ntfargo / ufm42 / DrYenyen** — CSSFontFace UAF discovery
- **Gezine / cheburek3000** — P2JB kernel exploit
- **Y2JB Framework** — JavaScript loader for PS5
