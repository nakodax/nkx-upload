# nkx-upload: encrypted upload for large video and audio recordings

**nkx-upload is a command-line tool from [NakodaX](https://nakodax.com) that encrypts video and audio recordings on your computer and uploads them to your [NakodaX box](https://box.nakodax.com) drive.** It takes files up to 5 GB, which is past the browser's 500 MB limit. It runs on macOS, Windows and Linux.

Recordings are encrypted before they leave your computer. Once uploaded, a recording plays and shares from box like any other protected document, and you decide who can open it and for how long.

> **Status:** the first public release is on its way. The install commands below work once it is published.

## About NakodaX

NakodaX helps you keep control of sensitive work after you share it. It protects documents, media, data and software with encryption and permission checks at the point of use, so you can change, limit or revoke access at any time, even after content has been shared or downloaded. NakodaX does not store or access the readable version of your content.

**NakodaX box** is the document-protection product: protect contracts, presentations, PDFs, spreadsheets and recordings, then share them with the people who need them.

- [nakodax.com](https://nakodax.com)
- [Document protection](https://nakodax.com/documents)
- [Open NakodaX box](https://box.nakodax.com)

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

Or with [Scoop](https://scoop.sh):

```powershell
scoop bucket add nakodax https://github.com/nakodax/scoop-bucket
scoop install nakodax/nkx-upload
```

No admin rights are needed. The install script checks the download against the release's `SHA256SUMS` before installing it:

- **macOS and Linux:** `~/.local/bin`
- **Windows:** `%LOCALAPPDATA%\nkx-upload`, added to your user `PATH`

## Quick start

```bash
nkx-upload login                          # once every 30 days
nkx-upload upload "board meeting.mov"     # one or more files
```

1. `login` opens your browser. Sign in to box, check the code matches the one in your terminal, and press **Approve**.
2. `upload` encrypts each file on your computer, uploads it and prints a link to open it in box.

## Commands

| Command | What it does |
|---|---|
| `nkx-upload login` | Sign in through your browser. Add `--no-browser` on a machine without one. |
| `nkx-upload upload <files…>` | Encrypt and upload video or audio files, up to 5 GB each |
| `nkx-upload status` | Show who you are signed in as, and when the sign-in ends |
| `nkx-upload logout` | Sign out on this computer, and end the sign-in in box |
| `nkx-upload update` | Install the latest version |
| `nkx-upload --version` | Print the version |

## Supported files

- **Video:** `.mov`, `.mp4`, `.m4v`, `.mkv`, `.webm`, `.avi`, `.wmv`, `.mpg`, `.mpeg`, `.ts`, `.3gp`
- **Audio:** `.mp3`, `.m4a`, `.aac`, `.wav`, `.flac`, `.ogg`, `.oga`, `.opus`, `.wma`, `.aiff`, `.aif`
- **Size:** up to 5 GB per file

The tool prepares each video so it starts playing in box before it has fully downloaded. It doesn't re-encode, so quality stays the same. This needs free disk space about the size of the file.

## Privacy and security

- **Encrypted on your computer.** Files are encrypted with AES-256 before any part of them is uploaded, and they stay encrypted in storage.
- **No password in the terminal.** You sign in in your own browser and approve a short code, so the tool never sees your password.
- **Upload only.** The sign-in can add recordings to your drive and nothing else. It can't open, download, share or delete anything.
- **Ends on its own.** A sign-in lasts 30 days, and changing your box password ends it early. You can end it any time in box under **Settings → Profile → Command-line sign-ins**.
- **Stored privately.** The sign-in is saved to your own config folder, readable only by your user account:
  - macOS and Linux: `~/.config/nkx-upload`
  - Windows: `%APPDATA%\nkx-upload`

## If your connection drops

The tool keeps going. It picks up from where it stopped, and it waits up to an hour for the network to come back. Press **Ctrl-C** to cancel; nothing is kept.

If you close the terminal or restart your computer mid-upload, run the command again.

## Verify a download

Every release file is signed with [Sigstore cosign](https://docs.sigstore.dev). To check a file yourself:

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

- **Update:** run `nkx-upload update`, `brew upgrade nkx-upload` or `scoop update nkx-upload`. The tool tells you when a newer version is out.
- **Uninstall:** run `nkx-upload logout`, then delete the binary and the config folder. With Homebrew or Scoop, run `brew uninstall nkx-upload` or `scoop uninstall nkx-upload` instead of deleting the binary.

## FAQ

### How do I upload a video larger than 500 MB to NakodaX box?

Install nkx-upload, run `nkx-upload login` once, then run `nkx-upload upload <file>`. The browser upload in box stops at 500 MB; nkx-upload takes recordings up to 5 GB.

### Is nkx-upload free?

Yes. The tool is free to use with a NakodaX box account. Each file you upload counts toward your plan, the same as an upload in the browser.

### Can NakodaX see my recording?

No. The file is encrypted on your computer before it is uploaded, and it stays encrypted in storage. Only you and the people you share it with in box can play it.

### Can someone who steals my sign-in read my files?

No. A command-line sign-in can only upload. It can't open, download or share anything. If you think a sign-in has been exposed, end it in **Settings → Profile** and it stops working straight away.

### What happens if my internet connection drops during an upload?

The upload pauses and picks up from where it stopped when the connection comes back, for up to an hour. Press Ctrl-C to cancel; nothing is kept.

### Which operating systems does nkx-upload support?

macOS on Apple silicon and Intel, Windows 10 and 11 (64-bit), and Linux on x64 and arm64.

### Why install with a command instead of a download link?

A file installed by a command doesn't get the "downloaded from the internet" mark that makes macOS and Windows block apps. The install script checks each file against the release's `SHA256SUMS` instead, and every file is also signed (see **Verify a download**).

### Does it work behind a company firewall?

Usually, yes. It only needs outbound HTTPS. Some managed devices block any app that isn't code-signed by Apple or Microsoft. If yours does, or you need the addresses to allow, [contact support](https://nakodax.com/support).

### Is nkx-upload open source?

No. This repository publishes signed release binaries only. Third-party components and their licences are listed in `THIRD_PARTY_NOTICES.txt` in each release.

## Support

- **Help and questions:** [nakodax.com/support](https://nakodax.com/support)
- **LinkedIn:** [linkedin.com/company/nakodax](https://www.linkedin.com/company/nakodax)
- **X:** [x.com/nakodax](https://x.com/nakodax)

## License

Proprietary software, © NakodaX. Use is governed by the [NakodaX terms of service](https://nakodax.com/terms) and [privacy policy](https://nakodax.com/privacy).
