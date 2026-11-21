# IQ Review for SDR#

Preview 1.0.1.0 · SDR# 1922 Beta · Windows x64 · .NET 10 / Airspy VDK.

A dockable recording review panel with a whole-recording overview, a zoomed timeline, precise time entry, fine steps, and A/B looping. The fine display offers an audio waveform envelope, an audio spectrogram, or a time ruler.

## Install

1. Finish any recordings and close the target SDR# instance.
2. Extract the **preview ZIP** into the installation folder containing `SDRSharp.exe`, preserving its `Plugins` folder. The plugin DLL belongs at `Plugins\SDRSharp.IQReview\SDRSharp.IQReview.dll`.
3. Keep one copy of `SDRSharp.IQReview.dll` under Plugins. Move old copies to a backup outside the Plugins tree before updating.
4. Open SDR#, choose **Baseband File Player**, and load a compatible IQ recording.
5. Open **Menu → Plugins → Navigation : IQ Review**. Leave Enabled checked.

The preview ZIP is for installation and sharing with testers. The source ZIP is for developers. Packages contain no SDR# runtime, VDK reference libraries, recordings, receiver profiles, private addresses, or personal settings.

## Reviewing a recording

- The upper ruler spans the whole recording. Its outlined window shows which part is expanded below. Click or drag to choose another recording position.
- The lower ruler expands the timeframe selected in **View**: 5 seconds, 20 seconds, 1 minute, 5 minutes, 15 minutes, 1 hour, or the whole file. The view follows playback when the cursor reaches its edge.
- Enter **HH:mm:ss** or **HH:mm:ss.fff** in Time and click **Go** or press Enter. A date can be included as **yyyy-MM-dd HH:mm:ss.fff** when navigating across several dates. Clock-only entry selects the nearest matching date around the displayed recording position.
- Choose **UTC**, **Local**, or **Elapsed**. UTC and Local use the recording's timestamp. Local display uses the reviewing PC's time zone and the historical offset at that instant. Elapsed counts from the recording's beginning and accepts hours above 23.
- **−10 s**, **−1 s**, **+1 s**, and **+10 s** step through recording time. A paused seek remains paused. **Play** starts or resumes native playback, **Pause** holds the current position, and **Stop** pauses, releases the loop, and returns to the beginning.
- Type loop start **A** and end **B**, then press Enter or leave the field. Alternatively use **A at cursor**, **B at cursor**, or drag the separate top/bottom handles on the fine timeline.
- **Loop on** repeats the selected range through the native player, including when the panel is behind another docked tab. Turning Loop off restores the full recording range. Seeking outside an active loop releases that loop so the requested position is reachable.
- Setting A beyond the old B moves the range forward; setting B before the old A moves it backward. Boundary edits cannot produce a reversed or empty loop.
- **Options…** contains the display type, layout, and recording time-zone settings. Auto layout reflows for a narrow dock; Stacked retains the narrow layout in a wide area. The graphs stretch with their dock. The minimum panel width is 320 pixels; narrow docks use the stacked arrangement.

The time field can request millisecond positions, converted to IQ sample units. Actual audio output includes SDR# buffering and sound-device latency. Native loop transitions occur at the player's input-block boundaries. This preview does not promise sample-exact audible cuts or use the UI timer to implement looping.

## Audio waveform and spectrogram

The audio displays fill **as the recording plays**. Unplayed areas are empty; an empty area does not establish silence. This preview does not scan or demodulate unplayed portions of the IQ recording in the background.

The waveform is a peak envelope of the demodulated audio, rather than an RF strength measurement. It reflects SDR#'s demodulator, AGC, filters, CCC, noise processing and squelch before the monitoring hook. Speaker volume and mute do not disable this hook. Left and right are analyzed separately, so binaural or opposite-polarity audio is not canceled by summing the channels.

The audio spectrogram runs from 0 Hz to the smaller of 5 kHz or the audio Nyquist frequency. Brighter bins indicate stronger audio components in either channel. Its intensity is relative audio level, not calibrated RF power or SNR. It uses native windowed FFTs with selectable detail: 256, 512 (default), 1024, or 2048 points. Higher detail improves frequency separation at the cost of a longer analysis window; actual frequency resolution also depends on the audio output sample rate.

History is held in memory for the current file and tuning configuration. Changing files or the tuned channel resets it. Bins are at least 50 ms wide; for long recordings they become coarser to keep history bounded at 16,384 time bins and 32, 64, 128, or 256 spectral bands depending on detail (about 0.6–4.3 MB of data). The bin interval is shown under Options. No history sidecar is written. Under heavy load, display blocks can be skipped and the panel reports that count; navigation and recorded IQ samples are unaffected.

Audio trace timing follows the file player's input position and is approximate because the source can read ahead of the audio pipeline. The trace is a navigation aid, not a calibrated timing measurement. RAW or bypassed demodulation provides no usable audio history; the time controls remain available.

## File navigation

**Open…** brings up a file chooser. **Previous** and **Next** move through the current folder's `.wav`, `.rf64`, `.raw`, and `.ddc` files in filename order, ignoring sidecar JSON and text files. File names that sort by recording date/time therefore follow that order. The counter shows **File X of Y**; boundary buttons are disabled and navigation does not wrap. Folder contents are refreshed on file changes. Subfolders are not traversed.

The next file starts at its beginning. Playing stays playing; paused/stopped playback stays stopped. Each change resets the timeline, A/B selection, loop, and audio history. The listening frequency is retained when the entire selected channel fits inside the new recording's IQ bandwidth. Otherwise SDR#'s file center is used. Mode, binaural/stereo, and channel-filter width are retained. You can review all files on one frequency, retune, and browse back through them on another.

Extensions identify candidates, not a guarantee that their contents are valid IQ. Unrecognized headers may bring up SDR#'s native RAW sample-format configuration dialog. Native SDR# decoding determines compatibility; this plugin does not add Linrad RAW support or bypass an unsupported container.

## Display options

Under **Options**, choose an SDR# theme, cyan, amber, green, or heat-map color scheme; soft, normal, or strong contrast; and display gain of −12, 0, +12, or +24 dB. These choices redraw existing history. Display gain changes the trace and spectrogram brightness only; it does not change received audio, RF gain, or IQ samples. The surrounding controls follow SDR#'s theme.

**Detail** selects the FFT size and stored spectral-band count. Changing it clears the current audio history so different resolutions are not mixed; play the section again to refill it. The time axis is still controlled independently by **View**. Higher FFT detail does not provide sample-accurate audio timing or populate unplayed sections. These preferences are saved with IQ Review's other settings.

## Recording dates and time zones

Supported filename dates include `SDRSharp_20261118_005700Z_920000Hz_...wav` and ISO UTC timestamps. A `Z` timestamp is treated as UTC. A time-only name such as `05-40-00_162473418Hz.wav` inside a dated folder such as `2025_04_14` is interpreted in **Recording zone**.

The recording zone defaults to the PC's zone. On first use, an existing Clock plugin recording-zone choice is reused if available. Your own IQ Review setting is retained thereafter. Choose the zone of the PC that made the recording when it differs from the reviewing PC.

Historical daylight saving offsets are applied. Nonexistent spring-forward times are rejected. For an ambiguous fall-back filename, choose the first or second occurrence under **Repeated DST hour**. UTC filenames avoid this ambiguity. If a recording timestamp cannot be established, the panel uses elapsed time and explains why; it never substitutes the current live date/time.

## Compatibility and preview limits

IQ Review controls SDR#'s built-in Baseband File Player. The VDK has no general seek/loop interface, so a small adapter binds the native player's public, typed properties after checking their signatures. Unsupported source types or changed API signatures disable navigation with an explanation. The full-path fallback used for dated folders is read-only. A future SDR# update may require an adapter update even if its version label is unchanged.

IQ formats and large-file support are supplied by SDR#'s player; IQ Review does not rewrite or convert recording files. This plugin does not open Linrad RAW recordings itself. Testing used compatible IQ WAV copies of the supplied Linrad recordings. Live-radio recording and scheduling remain the job of SDR# and the IQ Scheduler plugin.

Select **Baseband File Player** as the source once, then use IQ Review's **Open…** button to choose recordings. It does not switch away from a live receiver automatically. File opening uses a checked adapter to SDR# 1922's native file loader and pauses the stream before replacing the reader. If SDR# is still calculating its own file overview, the panel asks you to try again once that finishes. An invalid file produces an error and attempts to restore the previous recording and position. Future SDR# changes to these internal members may require a plugin update.

## Helpful tester reports

Native validation of 1.0.1 in SDR# 1922 used 16-bit PCM IQ WAV excerpts from the supplied Linrad channel-A recording, tuned to 760 kHz in DSB with binaural enabled and a 10 kHz channel filter. Checks passed for the dedicated Play/Pause/Stop buttons; paused millisecond seeks; opening files; Previous/Next folder order and boundaries; continuing or pausing playback across file changes; retaining tuning/mode/binaural/filter settings; recovering the previous file and position after a malformed WAV; display changes retaining history; all four native FFT detail sizes; settings serialization and saving on clean exit; and responsive horizontal/stacked controls including the options drawer. Native control renders were checked at 1100 × 400 and 320 × 600 pixels; control bounds were also checked at 800 pixels wide. The stereo monitor left all input samples unchanged and retained a 1 kHz tone in opposite-polarity L/R channels at 2048-point detail. No audio-display queue drops were observed in this run. Release build: 0 warnings, 0 errors.

These are first-pass integration results, rather than an unattended soak test across every SDR# build, display scale, source configuration or IQ container variant. Audio history for unplayed sections remains outside this preview.

Include SDR# and plugin versions, file format, sample rate, duration, detector mode, binaural/CCC settings, output-device rate, panel size/layout, and the steps taken. Report differences between typed positions, the playhead and audible timing, loops that do not release, damaged labels in narrow docks, missing traces, or unexpectedly changing receiver settings. Do not share private receiver connection details.

## Build from source

Place the `IQReview` source folder alongside `lib` in an Airspy VDK checkout. Install the .NET 10 SDK and prepare a test SDR# as described by the VDK, then close that deployment target and build:

```powershell
dotnet build .\IQReview\SDRSharp.IQReview.csproj -c Release -p:SDRSharpDir="C:\Path\To\SDRSharp\"
```

SDR# supplies the runtime libraries; references are compile-time only. No external NuGet dependency is needed. Validation instrumentation and runtime copies are excluded from the source ZIP.

## License

See `LICENSE.txt` for the Airspy VDK license and its Airspy ecosystem restriction. Include it when redistributing this SDR# plugin.

