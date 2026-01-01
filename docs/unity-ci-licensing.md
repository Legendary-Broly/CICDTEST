# Unity CI licensing for GameCI

## Why the Sanity workflow failed
- Unity Personal does not use serial numbers. Supplying `UNITY_EMAIL`/`UNITY_PASSWORD` makes the GameCI runner attempt legacy serial-based activation, which Unity responds to with `Licensing::Client Error: Code 20110 (serial invalid)`.
- The GameCI runner expects a Unity license file (`Unity_lic.ulf`) encoded as base64 in the `UNITY_LICENSE` secret when using manual activation.

## Correct activation method (Unity 6000.3.2f1 Personal)
1. On a machine where Unity Personal is already activated, locate the license file:
   - macOS: `~/Library/Application Support/Unity/Unity_lic.ulf`
   - Windows: `%ProgramData%\Unity\Unity_lic.ulf`
   - Linux/Hub: `~/.local/share/unity3d/Unity/Unity_lic.ulf`
2. Base64-encode the `Unity_lic.ulf` contents and store the result in the `UNITY_LICENSE` GitHub secret (do **not** paste raw XML or a serial key).
3. In the GameCI workflow, enable manual activation and pass the secret via the `unityLicenses` input.

## Minimal working workflow snippet
```yaml
- name: Run EditMode tests (Unity)
  uses: game-ci/unity-test-runner@v4
  with:
    activationMode: manual
    unityLicenses: ${{ secrets.UNITY_LICENSE }} # base64 of Unity_lic.ulf
    unityVersion: 6000.3.2f1
    testMode: editmode
    githubToken: ${{ secrets.GITHUB_TOKEN }}
```

## Required workflow/environment changes
- **Remove** `UNITY_EMAIL` and `UNITY_PASSWORD` to avoid serial-based activation attempts.
- **Ensure** `UNITY_LICENSE` contains a base64-encoded `Unity_lic.ulf` from an already activated Personal installation.
- No serial key is needed or supported for Unity Personal in headless CI.
