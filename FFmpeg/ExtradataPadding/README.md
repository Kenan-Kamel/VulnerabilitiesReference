# FFmpeg extradata padding issues

This folder documents three FFmpeg extradata-padding vulnerabilities that I originally reported to the FFmpeg security team.

The issues involve insufficient padding of extradata buffers before later bitreader access, leading to heap out-of-bounds reads (CWE-125) and crash / denial-of-service conditions under crafted inputs.

The upstream fixes were merged into FFmpeg master in PR #22988 ("Fix various extradata padding issues") and related commits:

- f3c92329e9291999542664b76a6500927a78b43b
- 2032efe9ebb2d030ff257d72013df00f137e7766
- f3c92329e9291999542664b76a6500927a78b43b

- Links:
(https://git.ffmpeg.org/gitweb/ffmpeg.git/commit/23227a444de4a8f7696f46660cdd044b460f7e47)
(https://code.ffmpeg.org/FFmpeg/FFmpeg/pulls/22988)
(https://git.ffmpeg.org/gitweb/ffmpeg.git/commit/8439e0203744a30d280668fcd086f74ed5001da1)


## Included vulnerabilities

1. **WMA encoder**
   - Component: `libavcodec/wmaenc.c`
   - Fix commit: `f3c92329e9`
   - https://git.ffmpeg.org/gitweb/ffmpeg.git/commit/23227a444de4a8f7696f46660cdd044b460f7e47
   - Summary: missing required padding in WMA extradata allocation paths
   - The commit that introduced the issue is (d2a4e4b9cc9a0c2661e1c1d6f6b51babac2cec1b), back in 2014.
   - This affects version 2.4 until the fix is committed in master at 8.2
   - Crashes:- refer to the folder [AsanCrashes Report group 3](./AsanCrashesReports/group_3_wma)

2. **MOV**
   - Component: `libavformat/mov.c`
   - Function: `mov_read_iacb`
   - Fix: merged in PR #22988 / master commit `016a241102`
   - Summary: insufficiently padded extradata in the MOV parsing path
   - Crashes:- refer to the folder [AsanCrashes Report group 2](./AsanCrashesReports/group_2_iamf_mov)

3. **IAMF writer**
   - Component: `libavformat/iamf_writer.c`
   - Functions: `fill_codec_config`, `update_extradata`
   - Fix: merged in PR #22988 / master commit `016a241102`
   - Summary: copied extradata lacked required padding before GetBitContext-based access
   - The commit that introduced the issue was originally added in  
   - Crashes:- refer to the folder [AsanCrashes Report group 1](./AsanCrashesReports/group_1_iamf)

For the full crashes, please refer to [Full crashes](./AsanCrashesReports/Asan_Full_crashes_ffmpeg_bugs.txt)
## Disclosure/ MITRE submitter

- Original reporter/found by: **Kenan Alghythee**
- Upstream PR includes: `Reported-by: Kenan Alghythee <kalghy2@uic.edu>`
- After the fixes were merged, FFmpeg requested that CVE number(s) be obtained from MITRE for listing on FFmpeg's security page(email)
## Credits
- Kenan Alghythee — University of Illinois Chicago
- Xiaoguang Wang — University of Illinois Chicago
- Zechun Cao — Texas A&M University–San Antonio
- Nico Gibson — Texas A&M University-San Antonio
- Hang Zhang — Indiana University Bloomington
- Farhan Saif — University of Illinois Chicago
- Roberto Bertolini — University of Illinois Chicago

## Notes

Initial analysis identified additional related crash sites during review of extradata padding behavior. However, the upstream merged fixes and CVE request scope are limited to the three vulnerabilities above. The total found in relation to the CVEs is 6, and only 3 represent unique vulnerabilities. However, we included all 6 for future reference. 
