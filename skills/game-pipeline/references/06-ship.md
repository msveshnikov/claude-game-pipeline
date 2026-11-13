# 06 — Finish every task: commit, build, deploy, verify

Standing rule: when a task is finished, ship it without asking.

1. **Check**: run the autotest (and a screenshot tour for visual changes) and look at the screenshots.
2. **Commit everything** with a message that says what changed and why. Untracked leftovers that belong
   to the project go in too (a separate commit if unrelated). Never commit secrets or keystores.
3. **Import** new files if any: `godot --headless --path . --import`.
4. **Export all targets** (can run in the background; ~5 min):
   ```
   godot --headless --path . --export-release "Windows Desktop" build/windows/<Game>.exe
   godot --headless --path . --export-release "Web" build/web/index.html
   godot --headless --path . --export-debug "Android" build/android/<Game>.apk
   ```
   Check that each file has a fresh timestamp and that the log has no errors.
5. **Upload** the Web build and the APK, e.g.
   `scp build/web/* build/android/<Game>.apk user@host:/var/www/<site>/<slot>/`
   and confirm with `curl -sI https://<site>/<slot>/index.pck` (size and Last-Modified).
   The web server must serve `.wasm` as `application/wasm`; cross-origin isolation headers are only
   needed for threaded builds.
6. **Open the live page** in the browser, start a game, and check the thing you changed (and the console
   for errors).
7. Tell the user: commits, build sizes, the live URL, and what you verified.

## GitHub release (downloadable exe + APK)

For milestone builds, or when the user asks for downloads on GitHub (not every task), attach the
Windows exe and the APK to a GitHub release. The exe is a single file with the `.pck` embedded, so
it needs no zip; GitHub's per-asset limit is 2 GB.

1. Bump `version/code` and `version/name` in the Android preset when this is a new release
   (Android refuses to update an installed APK with the same or a lower `version/code`).
2. Export fresh builds (step 4), commit and push, then:
   ```
   gh release create vX.Y build/windows/<Game>.exe build/android/<Game>.apk \
     --target main --title "<Game> X.Y" --notes-file notes.md
   ```
   Notes: what's in the build, the web URL, and install hints (SmartScreen "More info → Run anyway"
   for the unsigned exe; "install unknown apps" for a debug-signed APK).
3. Verify: `gh release view vX.Y --json assets` shows both assets `uploaded` with the local sizes, and
   `https://github.com/<owner>/<repo>/releases/latest/download/<Game>.exe` answers 200.
4. Link `releases/latest` from the project README next to the web URL.

Release assets and tags are public on a public repo; creating one counts as a push.

Pushing to the git remote is separate: push only when the user asks (or when their project rules say so).

Keep the deploy target and the Godot/Blender paths in the project README and in memory so the next
session can ship without rediscovering them.
