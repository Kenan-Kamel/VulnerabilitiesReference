# FFmpeg extradata padding issues

This folder documents three FFmpeg extradata-padding vulnerabilities that I originally reported to the FFmpeg security team.

The issues involve insufficient padding of extradata buffers before later bitreader access, leading to heap out-of-bounds reads (CWE-125) and crash / denial-of-service conditions under crafted inputs.

The upstream fixes were merged into FFmpeg master in PR #22988 ("Fix various extradata padding issues") and related commits.

## Included vulnerabilities

1. **WMA encoder**
   - Component: `libavcodec/wmaenc.c`
   - Fix commit: `f3c92329e9`
   - Summary: missing required padding in WMA extradata allocation paths

2. **MOV**
   - Component: `libavformat/mov.c`
   - Function: `mov_read_iacb`
   - Fix: merged in PR #22988 / master commit `016a241102`
   - Summary: insufficiently padded extradata in MOV parsing path

3. **IAMF writer**
   - Component: `libavformat/iamf_writer.c`
   - Functions: `fill_codec_config`, `update_extradata`
   - Fix: merged in PR #22988 / master commit `016a241102`
   - Summary: copied extradata lacked required padding before GetBitContext-based access

## Disclosure / attribution

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

Initial analysis identified additional related crash sites during review of extradata padding behavior. However, the upstream merged fixes and CVE request scope are limited to the three vulnerabilities above. The total found in relation to the CVEs is 6, and only 3 represent a distant vulnerabilities.
