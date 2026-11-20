# MADANY RUN 3D
Pitch: Dash through a bright Cairo street, dodge carts and trains, and scoop up gold.
Core verb: Dodge.
Loop: Read the next street hazard, switch lanes or jump/slide, and collect a coin line. Speed rises steadily from 8 to 23 world units/s, then levels off.
Lose/win: A collision ends the run; beat the saved best. Opening is clear for several seconds.
Difficulty: Starts at 8 world units/s, rises toward 16 over 90 seconds.
Controls: Swipe horizontally to change lanes, swipe up to jump, down to slide; bottom buttons offer jump and slide.
Look: Sunlit toy Cairo, terracotta and turquoise facades, upper-floor windows, market awnings, street lamps, dashed lane guidance, cream road, teal commuter trains, hand-built geometric runner. Warm noon light; gold is reward, coral is danger.
Feel: Snappy lane snaps, springy jump, duck-under train, coin pickup pulse, quick retry.
Sound: Synthesized short wooden taps, metallic coin chime, low collision thud; mute toggle.
Shell: Animated street on title and results; best distance and coin count persist.
Icon: Original runner silhouette with gold coin on a turquoise city backdrop.

Stage 4 visual pass: metro cars now include clearer sliding-door frames, paired door panels, grab rails, roof equipment and a pantograph silhouette; the front cab gains bumper and wiper accents. The pursuer gains uniform details and a chest badge, while the dog gains a bright collar and tag. Android version metadata is now 1.1 (versionCode 2).


Stage 5 gameplay polish: lane-change collision now checks the runner's swept world-space position during the frame instead of relying only on the immediately selected lane. This makes obstacle and coin interactions more consistent while the runner is visibly transitioning between tracks. Swipe thresholds are now density-independent (dp-based) in both touch surfaces, and the OpenGL surface requests an 8-bit RGBA color buffer for more consistent color output on supported devices. Android version metadata is 1.2 (versionCode 3).


## Version 1.3 animation update
- Runner stride now uses alternating eased foot-lift and knee-flex curves rather than only a symmetric sine offset.
- Arm swing counter-rotates against the legs for a more readable sprint silhouette.
- Added smooth slide-blend and gait helper functions with unit tests.
- This is a procedural low-poly character rig; it is not a captured or imported Subway Surfers character/model.


## Version 1.4 chase animation update
- Pursuer now uses alternating leg phases, eased foot recovery, visible knee bends, and counter-swinging arms.
- Dog now uses a diagonal four-beat gallop with separate paw lift, body bounce, and an independently wagging tail.
- Character work remains procedural low-poly geometry; no external copyrighted game assets were added.
- Android version metadata is now 1.4 (versionCode 4).


## Version 1.5 gameplay and reliability pass
- Fixed swept lane-change collision distance: collision checks now measure the obstacle's distance from the actual segment traversed by the runner, rather than treating every obstacle between the endpoints as an automatic overlap.
- Daily mission now requires 250 m and 15 coins in the same run; wallet balance and historical best no longer incorrectly satisfy the current-run objective.
- Coin trails are biased toward a neighboring safe lane instead of routinely following the blocked lane.
- HUD jump/slide tap tolerance now uses density-independent pixels.
- OpenGL shader compilation and program linking are checked explicitly, with useful failure logs; depth buffer defaults are explicit.
- Android version metadata is now 1.5 (versionCode 5).


## Version 1.6 scoring and mission clarity
- The HUD and best-score labels now identify the displayed value as points (PTS), not meters.
- Daily mission distance is measured from actual forward-runner travel, independently of score bonuses, coin bonuses, or the double-score power-up.
- The mission screen shows current-run distance and coin progress.
- Obstacle randomness is no longer seeded to the same sequence on every launch.
- Audio shutdown now pauses playback and gives the audio worker time to exit before releasing the AudioTrack.
- Android version metadata is now 1.6 (versionCode 6).
