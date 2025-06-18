## v0.10.1

* Support setting `--enc-input hwaccel=none --enc-input hwaccel_output_format=none`: This allows you to omit default hardware acceleration settings for `*_vaapi` and `*_vulkan` video codecs introduced in `v0.9.4`.

## v0.10.0

* `--pix-format` no longer defaults to "yuv420p": If not specified, no `-pix_fmt` will be passed to ffmpeg, allowing the use of upstream defaults. However, `libsvtav1`, `libaom-av1`, and `librav1e` will continue to default to "yuv420p10le".
* Allow specifying ffmpeg decoder: Use `--enc-input c:v=CODEC`.

## v0.9.4

* Encoder `*_vaapi`: Defaults to `--enc-input hwaccel=vaapi --enc-input hwaccel_output_format=vaapi`.
* Encoder `*_vulkan`: Maps `--crf` to ffmpeg's `-qp`.
* Encoder `*_vulkan`: Defaults to `--enc-input hwaccel=vulkan --enc-input hwaccel_output_format=vulkan`.
* Encoder `libvvenc`: Maps `--crf` to ffmpeg's `-qp`.

## v0.9.3

* Support setting per-stream audio codec: For example, `--enc c:a:1=libopus`.
* Support `--pix-format yuv422p10le`.
* Write video stream metadata "AB\_AV1\_FFMPEG\_ARGS": Includes a subset of relevant ffmpeg arguments used (e.g., `AB_AV1_FFMPEG_ARGS: -c:v libsvtav1 -crf 25 -preset 8`). Note: Not supported by MP4 files.

## v0.9.2

* Log CRF results instead of printing: If `stderr` is not a terminal, results are logged.
* Wait for all child processes: Ensures `ffmpeg` and other processes finish before temporary file cleanup and exit.

## v0.9.1

* Fix XPSNR inf score parsing.
* Fix XPSNR reference vfilter usage.
* Add `--xpsnr-fps`: Frame rate override used to analyze both reference and distorted videos. Defaults to 60.

## v0.9.0

* Add XPSNR support as a VMAF alternative:
* Add `sample-encode --xpsnr` argument to toggle use of XPSNR instead of VMAF.
* Add `crf-search`, `auto-encode --min-xpsnr` argument (alternative to `--min-vmaf`).
* Add `xpsnr` command for measuring XPSNR score.
* Support negative `--preset` arguments.
* Add `--vmaf-fps`: Frame rate override used to analyze both reference and distorted videos. Defaults to 25.
* Omit data streams when outputting to Matroska (`.mkv` or `.webm`).
* Omit audio, subtitle, and data streams in VMAF calls: Works around possible `ffmpeg` memory leaks.
* `mpeg2video`: Maps `--crf` to `ffmpeg -q` and sets default CRF range to 2-30.

## v0.8.0

* `crf-search`: Tweaked 2nd iteration logic that slices the CRF range at the 25% or 75% CRF point.
* Widened to 20%/80% to account for searches of the "middle" two subranges being more optimal.
* Disabled when using custom min/max CRF ranges under half the default.
* Add `sample-encode` info to `crf-search` & `auto-encode`: Shows sample progress and encoding/VMAF FPS.
* Improve `sample-encode` progress format consistency.
* Add `crf-search -v` flag: Prints per-sample results.
* Add `auto-encode -v` flag: Prints per-CRF results; `-vv` also prints per-sample results.

## v0.7.19

* Fix stdin handling: Resolved an issue that sometimes broke bash shells.

## v0.7.18

* Use default `.265`, `.264` image output extensions for `libx265`, `libx264`: Fixes `crf-search` for images with these codecs.
* Improve `--vfilter` docs: Clarified VMAF usage.

## v0.7.17

* Improve failing `ffmpeg` `stderr` printing:
* Prevented many `\r`-ending updates from truncating other stored info.
* Increased max heap storage of output from \~4KB to 32KB to allow more complete output in some cases.
* Fix caching: Resolved an issue where caching was unaffected by `--reference-vfilter` usage.
* Improve `--vfilter` docs: Described VMAF usage and mentioned `--reference-vfilter`.
* Improve `--vmaf-scale` docs.
* VMAF: Removed `-r 24` `ffmpeg` input.
* VMAF: Added new default options "shortest=true", "ts\_sync\_mode=nearest", and use `vfilter "settb=AVTB"`.

## v0.7.16

* Fix VMAF score parse failure: Resolved an issue with certain successful `ffmpeg` outputs.

## v0.7.15

* Show full `ffmpeg` command after errors.
* For `*_vaapi` encoders: Map `--crf` to `ffmpeg -q` (instead of `-qp`).
* Set `av1_vaapi` default `--max-crf` to 255.
* Fix `sample-encode` printing output to non-terminals.
* Omit "Encode with: ..." `stderr` hint for non-terminals.
* Support logging enabled: When `stderr` is not a terminal or by setting env var `RUST_LOG`. For example:
* `RUST_LOG=ab_av1=info` "info" level logs various progress results like sample encode info.
* `RUST_LOG=ab_av1=debug` "debug" level logs include `ffmpeg` calls.
* Don't panic on non-zero status exit.
* Fail faster when unable to parse a VMAF score: Includes `ffmpeg` output.
* Add `--reference-vfilter` arg: For `_sample-encode_`, `_crf-search_`, `_auto-encode_` to allow overriding `--vfilter` for VMAF.
* Add `--sample-duration` arg: Configures the duration of each sample. Defaults to 20s.

## v0.7.14

* Fix bash completions of some filenames.

## v0.7.13

* Use a single `ffmpeg` process to calculate VMAF: Replaces multi-process piping.
* Exclude subtitle tracks from samples.
* Add `--keep` option: For `_crf-search_` & `_auto-encode_`.

## v0.7.12

* Improve ETA stability.

## v0.7.11

* Fix `sample-encode` caching: Now considers VMAF arguments.

## v0.7.10

* Fix validation: Prevented use of SVT args starting with "-i", "-b".

## v0.7.9

* Fix validation: Prevented use of `ffmpeg --enc` args starting with "-i", e.g., "-init\_hw\_device".

## v0.7.8

* Fix ETA calculation overflow panic scenario.

## v0.7.7

* Add `--video-only` option: For `_encode_` & `_auto-encode_`.

## v0.7.6

* Fix nested temporary directories not being cleaned properly.
* Temporary directories will now start with ".": Created in the working directory instead of the input parent (unless `--temp-dir` is set).

## v0.7.5

* Add `-e librav1e` support: Maps `--crf` to `ffmpeg -qp` (default max 255), `--preset` to `-speed` (0-10).
* Disallow `--enc svtav1-params=` usage: `libsvtav1` parameters should instead be set with `--svt`.

## v0.7.4

* Add `--encoder` support for QSV family of `ffmpeg` encoders: `av1_qsv`, `hevc_qsv`, `vp9_qsv`, `h264_qsv`, and `mpeg2_qsv`.
* Enable lookahead mode by default for encoders: `av1_qsv`, `hevc_qsv`, `h264_qsv`.

## v0.7.3

* Include all other non-main video streams: Copied instead of encoding them with the same settings as the main video stream.
* Always copy audio: Unless `--acodec` or `--downmix-to-stereo` are specified. Previously, it would re-encode to Opus when changing containers.

## v0.7.2

* Print failing `ffmpeg` `stderr` output.
* Preserve all input file streams: (e.g., audio, subs, attachments) into output.
* Support concurrent running processes out of the box: By segregating temporary directories and fixing cache access.
* Improve VMAF accuracy in some cases: By forcing 24fps and synchronizing the presentation timestamp.
* Automatically workaround `ffmpeg` "Can't write packet with unknown timestamp" sample generation failures: (typically encountered with old AVI files) by using `-fflags +genpts`.

## v0.7.1

* Fix `_crf-search_` incorrectly picking a rate that exceeds `--max-encoded-percent`.
* Improve `_auto-encode_` CRF float display rounding.

## v0.7.0

* Use `ffmpeg` for SVT-AV1 encodes: Instead of invoking `SvtAv1EncApp` directly. This unifies the handling of other encoders and allows SVT-AV1 encoding to benefit from more built-in `ffmpeg` behaviors like aspect preservation. An `ffmpeg` build with `libsvtav1` enabled is now required. `SvtAv1EncApp` is no longer required.
* Improve image detection.
* Add `--encoder` support for NVENC family of `ffmpeg` encoders: `av1_nvenc`, `hevc_nvenc`, and `h264_nvenc`.

## v0.6.1

* Add `_sample-encode_`, `_crf-search_`, `_auto-encode_` arg `--min-samples`.
* Revert `libvpx-vp9 --crf-increment` default to 1.

## v0.6.0

* Support decimal CRF values: In `_sample-encode_`, `_encode_` subcommands (note SVT-AV1 only supports integer CRF).
* Add `_crf-search_`, `_auto-encode_` arg `--crf-increment`: Previously this would always be 1. Defaults to 1. `-e libx264`, `libx265`, and `libvpx-vp9` default to 0.1.
* Add `_crf-search_`, `_auto-encode_` arg `--thorough`: More exhaustively searches to find a CRF value close to the specified `min-vmaf`.
* Cache `_sample-encode_` results: In `$CACHE_DIR/ab-av1` directory. This allows repeated same CRF sample encoding to be avoided when running `_sample-encode_`, `_crf-search_`, and `_auto-encode_`. For example, repeating a `_crf-search_` with a different `min-vmaf`. Caching is enabled by default. Can be disabled with `--cache false` or setting env var `AB_AV1_CACHE=false`.
* Use MKV containers for all lossless samples: Previously MP4 samples were used for MP4 inputs; however, in all test cases, MKV 20s samples were better quality. This change improves accuracy for all MP4 input files.
* Default `--max-crf` to 46 for `libx264` & `libx265` encoders.
* Encode WebM outputs with the "cues" seek index at the front: To optimize stream usage (as done with MKV).

## v0.5.2

* Fix `ffprobe` duration conversion error scenarios panicking.
* Tweak encoded size prediction logic: Now considers both input file size and encoded sample duration.

## v0.5.1

* Change encoded size prediction logic: To estimate video stream size (or image size) only. This should be much more consistent than the previous method. Changed `_crf-search_`, `_sample-encode_` result text to clarify this.
* Improve video size prediction logic: Accounts for samples that do not turn out as 20s.
* Fix full-pass sample encode progress bar.
* Use label "Full pass" instead of "Sample 1/1": When doing a full pass `_sample-encode_`.
* Add VMAF auto model, n\_threads & scaling documentation.

## v0.5.0

* Default to `.mkv` output format for all inputs: (except `.mp4`, which will continue to output `.mp4` by default). This also applies to `ffmpeg` encoder sample output format. The previous behavior used the input extension, which may not have supported AV1 (e.g., `.m2ts`).
* For `_auto-encode_`: Use the output extension also for `ffmpeg` encoder sample outputs if applicable.
* When creating lossless samples for encode analysis: Use `.mkv` (or `.mp4`) extension for better `ffmpeg` compatibility.
* Encode MKV outputs with the "cues" seek index at the front: To optimize stream usage.
* Optimize pixel format choice for VMAF comparisons: Can significantly improve VMAF FPS. For example, if both videos are `yuv420p`, use that instead of `yuv444p10le`.
* When sampling: Use full input video when sample time would be `>= 85%` of the total (down from 100%).
* Eliminate repeated redundant `ffprobe` calls.
* Windows: Support VMAF pixel format conversion for both distorted and reference. Gives more consistently accurate results and brings Windows in line with Linux functionality.
* Windows: `ab-av1.exe` binaries will now be automatically built and attached to releases.

## v0.4.4

* Add `_crf-search_`, `_auto-encode_`, `_encode_` & `_vmaf_` command support for encoding images into AVIF: Works in the same way as videos. Example: `ab-av1 auto-encode -i pic.jpg`. The default encoder `svt-av1` has some dimension limitations, which may cause this to fail. `-e libaom-av1` also works and supports more dimensions.
* Convert to `yuv444p10le` pixel format when calculating VMAF: For accuracy and compatibility.
* Update to `clap v4`: Changes help/about output and reduces binary size.
* Print `_crf-search_` attempts: Even when `stderr` is not a TTY.

## v0.4.3

* Fix terminal breaking sometimes after exiting early.

## v0.4.2

* Update `_indicatif_` dependency to `0.17`.

## v0.4.1

* For `-e libvpx-vp9`: Map `--preset` number to `ffmpeg -cpu-used` (0-5).
* When overriding with an `ffmpeg` encoder: Avoid setting `b:a`, `movflags`, or `ac` if explicitly set via `--enc`.
* Add error output: When using `--enc-input` with the default `svt-av1` encoder.
* Add errors for `--enc`/`--enc-input` args: That are already provided by existing args or inferred.

## v0.4.0

* Add `--encoder`/`-e` encoder override: Any [encoder `ffmpeg` supports](https://www.google.com/search?q=%5Bhttps://ffmpeg.org/ffmpeg-all.html%23toc-Video-Encoders%5D\(https://ffmpeg.org/ffmpeg-all.html%23toc-Video-Encoders\)) and that may be controlled using `-crf` may be used.
* Add `--enc $FFMPEG_ARG`: For providing arbitrary output options to the `ffmpeg` encoder invocation. These only work when overriding the encoder with `-e`. For example, setting x265 params: `-e libx265 --enc x265-params=lossless=1`.
* Add `--enc-input $FFMPEG_ARG`: For providing `ffmpeg` input file options, similar to `--enc`.
* `--preset` now supports word presets: Like `slow`, `veryfast` for `ffmpeg` encoders like `libx264`.
* `--preset` is no longer required. Default `svt-av1 --preset` is now 8.
* Support setting `keyint` for `-e` encoders: In a similar way as is done for AV1.
* Add default `vp9` & `libaom-av1 -b:v 0` setting: So constant quality CRF based encoding works consistently.
* For `-e libaom-av1`: Map `--preset` number to `ffmpeg -cpu-used` (0-8).
* For `*_vaapi` encoders: Map `--crf` to `ffmpeg -qp` as CRF is not supported.
* Shell escape file name in "Encoding ..." output.

## v0.3.4

* Shell escape file names when hinting commands.



## v0.3.3

* Show more info when `auto-encode` fails to find a suitable CRF.

## v0.3.2

* Improve sample generation speed & frame duration accuracy.

## v0.3.1

* Fix some cases where `ffmpeg` progress & VMAF score output parsing failed.
* Fix some edge cases where `crf-search` would succeed exceeding the specified `--max-encoded-percent`.

## v0.3.0

* Select VMAF model `model=version=vmaf_4k_v0.6.1`: For videos larger than 2560x1440 if no other model is specified. This will raise VMAF scores for 4k videos that previously were getting harsher treatment from the 1k model.
* Add `--vmaf-scale` option: Sets the video resolution scale to use in VMAF analysis. `auto` (default) auto scales based on model & resolution, `none` no scaling or custom `WxH` format, e.g., `1920x1080`.
* `auto` upscales 1728x972 & smaller to 1080p, preserving aspect, when using the default 1k VMAF model. This will reduce VMAF scores that previously were getting more generous treatment from the 1k model.
* `auto` upscales 3456x1944 & smaller to 4k, preserving aspect, when using the 4k VMAF model.
* Add `--downmix-to-stereo` option: If enabled and the input streams use \> 3 channels (DTS 5.1 etc.), downmix input audio streams to stereo.
* After encoding, print per-stream sizes: In addition to the file size & percent.
* Add predicted video stream percent reduction to `_auto-encode_` search progress bar: After a successful search.
* Support non-video/audio/subtitle streams from input to output: E.g., attachments.
* When defaulting the output file: Don't use input extension if it is `.avi`, `.y4m`, `.ivf`; use `.mp4` instead.
* Fix clearing `_crf-search_` progress bar output on error.
* Strip debug symbols in release builds by default: Reduces binary size (requires `rustc 1.59`).

## v0.2.0

* Add `svt-av1` option `--keyint FRAME-OR-DURATION` argument: Supporting frame integer or duration string. E.g., `--keyint=300` or `--keyint=10s`. Default `keyint` to `10s` when input duration is over 3m.
* Add `svt-av1` option `--scd true|false` argument: Enabling scene change detection. Default `scd` on when using default `keyint` & input duration is over 3m.
* Add `--svt ARG` for additional args: E.g., `--svt mbr=2000 --svt film-grain=30`.
* Add `--vfilter ARG` argument: To apply an `ffmpeg` video filter (crop, scale etc.) to the input before AV1 encoding. E.g., `--vfilter "scale=1280:-1,fps=24"`.
* Add `--pix-format ARG` argument: Supporting `yuv420p10le` (default) & `yuv420p`.
* Add VMAF configuration `--vmaf ARG`: E.g., `--vmaf n_threads=8 --vmaf n_subsample=4`.
* Rename `_vmaf_` command argument `--reference` (was `--original`).
* Add `_vmaf_` command `--reference-vfilter` argument: Similar to `--vfilter`.
* Default VMAF `n_threads` to the number of logical CPUs.
* Add `--temp-dir` argument: To specify storage of sample data. May also be set with env var `AB_AV1_TEMP_DIR`.
* Add `--sample-every DURATION` argument: Default "12m".
* Remove 3 sample default: This is now calculated using `--sample-every` 12m default.
* Create samples concurrently: While encoding to reduce I/O lags waiting to encode.
* `_crf-search_` re-use samples for CRF analysis.
* Linux: `_vmaf_` uses FIFO to convert both reference & distorted to YUV, which fixes VMAF accuracy in some cases.
* Support multiple audio & subtitle streams.
* Use 128k bitrate as a default for `libopus` audio.
* Remove `--aq`.
* Fail fast if `ffmpeg` cut samples are empty (`< 1K`).
* Handle input durations lower than the sample duration: By using the whole input as a single sample.

## v0.1.1

* Add command to generate bash, fish & Zsh completions: `ab-av1 print-completions [SHELL]`.

## v0.1.0

* Initial release.
