# nkx-upload: encrypted upload for large video and audio recordings

**nkx-upload is a command-line tool from [NakodaX](https://nakodax.com) that encrypts video and audio recordings on your computer and uploads them to your [NakodaX box](https://box.nakodax.com) drive.** It takes files up to 5 GB, which is past the browser's 500 MB limit. It runs on macOS, Windows and Linux.

Recordings are locked with AES-256-GCM before they leave your computer, and NakodaX never sees them unlocked. Once uploaded, a recording plays and shares from box like any other protected document.

> **Status:** the first public release is on its way. The install commands below work once it is published.

## Install

**macOS and Linux**

```bash
curl -fsSL https://box.nakodax.com/install.sh | sh
```

Or with Homebrew:

```bash
brew install nakodax/tap/nkx-upload
```

**Windows (PowerShell)**

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://box.nakodax.com/install.ps1 | iex"
```

No admin rights are needed. The script checks the download against the release's `SHA256SUMS` before installing it:

- **macOS and Linux:** `~/.local/bin`
- **Windows:** `%LOCALAPPDATA%\nkx-upload`, added to your user `PATH`

## Quick start

```bash
nkx-upload login                          # once every 30 days
nkx-upload upload "board meeting.mov"     # one or more files
```

1. `login` opens your browser. Sign in to box, check the code matches the one in your terminal, and press **Approve**.
2. `upload` locks each file on your computer, uploads it and prints a link to open it in box.

## Commands

| Command | What it does |
|---|---|
| `nkx-upload login` | Sign in through your browser. Add `--no-browser` on a machine without one. |
| `nkx-upload upload <files…>` | Lock and upload video or audio files, up to 5 GB each |
| `nkx-upload status` | Show who you are signed in as, and when the sign-in ends |
| `nkx-upload logout` | Sign out on this computer, and end the sign-in in box |
| `nkx-upload update` | Install the latest version |
| `nkx-upload --version` | Print the version |

## Supported files

- **Video:** `.mov`, `.mp4`, `.m4v`, `.mkv`, `.webm`, `.avi`, `.wmv`, `.mpg`, `.mpeg`, `.ts`, `.3gp`
- **Audio:** `.mp3`, `.m4a`, `.aac`, `.wav`, `.flac`, `.ogg`, `.oga`, `.opus`, `.wma`, `.aiff`, `.aif`
- **Size:** up to 5 GB per file

Where it can, the tool repackages a video as a streaming MP4 so it starts playing before it has fully downloaded. It copies the audio and video as they are, so quality does not change and nothing is re-encoded. This needs free disk space about the size of the file, and it happens on your computer.

## How it keeps recordings private

- **Encrypted before upload.** Each file is locked with its own AES-256-GCM key, in the same format box uses in the browser.
- **Straight to storage.** The encrypted file goes directly to cloud storage over HTTPS; it does not pass through NakodaX's servers.
- **No password in the terminal.** You sign in in your own browser and approve a short code, so the tool never sees your password.
- **Upload only.** The sign-in can add recordings to your drive and nothing else. It cannot open, download, share or delete anything, including files it uploaded.
- **Ends on its own.** A sign-in lasts 30 days, and changing your box password ends it early. You can end it any time in box under **Settings → Profile → Command-line sign-ins**.
- **Stored privately.** The sign-in is saved only to your user account's config folder, readable by you alone:
  - macOS and Linux: `~/.config/nkx-upload`
  - Windows: `%APPDATA%\nkx-upload`

## If your connection drops

The tool keeps going. It resends from the point your connection dropped, and it waits up to an hour for the network to come back. Press **Ctrl-C** to cancel: nothing is kept, and the file uses no storage.

If you close the terminal or restart your computer mid-upload, run the command again.

## Verify a download

Every release file is signed with [Sigstore cosign](https://docs.sigstore.dev) in NakodaX's build pipeline, with no long-lived signing key. To check a file yourself:

```bash
cosign verify-blob \
  --certificate nkx-upload-darwin-arm64.pem \
  --signature nkx-upload-darwin-arm64.sig \
  --certificate-identity-regexp 'https://github.com/nakodax/.*' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  nkx-upload-darwin-arm64
```

Every release also has a `SHA256SUMS` file for a plain checksum.

## Update and uninstall

- **Update:** `nkx-upload update`, or `brew upgrade nkx-upload`. The tool tells you when a newer version is out.
- **Uninstall:** run `nkx-upload logout`, then delete the binary and the config folder. With Homebrew, run `brew uninstall nkx-upload` instead of deleting the binary.

## FAQ

### How do I upload a video larger than 500 MB to NakodaX box?

Install nkx-upload, run `nkx-upload login` once, then run `nkx-upload upload <file>`. The browser upload in box stops at 500 MB; nkx-upload takes recordings up to 5 GB.

### Is nkx-upload free?

The tool is free to download and use with a NakodaX box account. Each uploaded file uses one protection from your plan, the same as an upload in the browser.

### Can NakodaX see my recording?

No. The file is encrypted on your computer before any of it is uploaded, and it stays encrypted in storage. Only people you give access to in box can play it.

### Can someone who steals my sign-in read my files?

No. A command-line sign-in can only upload. It cannot open, download or share anything. If you think a sign-in has been exposed, end it in **Settings → Profile** and it stops working straight away.

### What happens if my internet connection drops during an upload?

The upload pauses and carries on from the same point when the connection returns, for up to an hour. Press Ctrl-C to cancel; nothing is kept.

### Which operating systems does nkx-upload support?

macOS on Apple silicon and Intel, Windows 10 and 11 (64-bit), and Linux on x64 and arm64.

### Why install with a command instead of a download link?

A file that is installed by a command, rather than downloaded in a browser, does not get the "downloaded from the internet" mark. That mark is what makes macOS and Windows block unsigned apps. The install script checks every file against the release's `SHA256SUMS` instead, and every file is also signed with cosign (see **Verify a download**).

### Does it work behind a company firewall?

It needs HTTPS access to `box-api.nakodax.com` and to `storage.googleapis.com`. Some managed devices block any app that isn't code-signed by Apple or Microsoft. If yours does, [contact support](https://nakodax.com/support).

### Is nkx-upload open source?

No. This repository publishes signed release binaries only. The tool uses [mediabunny](https://github.com/Vanilagy/mediabunny) (MPL-2.0) to repackage video.

## Support

- **Help and questions:** [nakodax.com/support](https://nakodax.com/support)
- **Use box in your browser:** [box.nakodax.com](https://box.nakodax.com)
- **About document protection:** [nakodax.com/documents](https://nakodax.com/documents)

## License

Proprietary software, © NakodaX. Use is governed by the [NakodaX terms of service](https://nakodax.com/terms) and [privacy policy](https://nakodax.com/privacy).
