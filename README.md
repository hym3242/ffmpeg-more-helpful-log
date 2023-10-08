# ffmpeg-more-helpful-log
A patch that adds more helpful log messages(with loglevel WARNING/ERROR) to ffmpeg for debugging or understanding ffmpeg. It also modifies some defaults to be more sane.

I am very bad at C and this is only for ad-hoc ffmpeg hacking, sorry for the messy code...

# Details
- **Detailed logs about options parsing**: what the parsed options are(help strings), applied to what OptionGroup/XXXContext or is a func_arg, and is consumed by whom at what time.
- **Detailed logs about format conversions**: how the AVFrame format changes along the filterchain, and what yuv2rgb matrix is used to do the conversions.
- when and what FFCodec is initialized, when and what encoder/decoder/muxer/demuxer threads are created.
- add NAL sizes(by bytes) to `-loglevel trace` output
- **better vf_showinfo**: also dumps the frame metadata, ASCII unregistered SEI messages, more verbose lines, also with comparison of deprecated pkt_pos and pkt_pos from ffmpeg FrameData(mentioned in the deprecation commit message).
- some modifications regarding DolbyVision(DOVI) muxing and stream codec tag finding.
- various modifications to log messages to make them easier to understand without digging into source code.

# Behavioral changes
- In auto_scale filters that do yuv<->rgb conversion, when the input AVFrame's colorspace is BGR or UNSPECIFIED, use bt709 instead of bt470bg. Otherwise yuv frames with bt709 colorspace would get wrong colors after going through yuv->rgb->yuv in  filtergraphs.
- Made an explicit exception for the fourcc tag `dvh1` and HEVC codec id. Otherwise ffmpeg would refuse to tag hvc1 stream as dvh1 in Dolby Vision muxing.
