# Player pictures when held still, walking and running

Titles: `title-1`

Which picture the leading character shows on the frames where it is held
still: the stop after a roll, the frames after a jump's landing before
walking, running or standing starts, and a bounce's two frozen frames.
Also the pictures just before and after these that are needed to place
them, and the first character's pictures while walking, running and
turning on the ground and in the air after walking or running off a
ledge without a face, while rolling, and in the air after a bounce.

## The pictures

Each picture has a label used in this file, and is named by its picture
hash (`../formats/sprites.md`, "Picture hashes"). "Frame" is a logic
frame at 60 Hz, numbered as in a trace. The picture given for a logic
frame is the one on screen with that frame's view
(`../formats/sprites.md`, "Where the picture is drawn"). It is mirrored
left–right when the player faces left, and facing follows the behavior
files on every frame below. In the lists, "`stop` × 3" means `stop` on
3 frames in a row.

The colours each character's pictures are drawn with are in
`../formats/sprites.md`, "The characters' palettes".

| First character | Picture hash (SHA-256) |
|---|---|
| `roll-1` | `d1293eefe586b6d7c5a8206301e9732a4b325de17de865693bb031da337f0ad3` |
| `roll-2` | `22aee199cd0021b2d3aaf0d9a5c94a8ce9b7e2c0cf673a9260f0e57aa82c8507` |
| `roll-3` | `440441b302e863b5196d6ea694ecbe51741e6cfd1e05b955b858f3cd8b54cf90` |
| `roll-4` | `2047f847707e77d5cc284d43a5df149c615a53bec99cc9340321a287da1e5352` |
| `roll-5` | `e0c36ff720d8dcd46f0b558d54e84c3f7e5a1ad038df0a548fba39a54cd7cda8` |
| `roll-6` | `c1cbbc6557606bbf7e94d694a9b3ed51902ddae2e58c4e53df3de6ae40bc2f30` |
| `roll-7` | `3ae1e0344b600798af6eb10e4e1a86ddb4a28fd55e3912f8a81b16cf99cd670f` |
| `roll-8` | `2d7740e2363b34945b5c1a3b8ef8dbde1a8154799757a37a37f63adb0560d0d9` |
| `roll-9` | `17f2a4a92f37caf7ff88e5412f36496d92b3699ef92f4dac1c9d39d148db4ca1` |
| `roll-end` | `bfa91eacc23022b5cf106533b4a0aa3baebcb414263d38c2dc0c774d8e1d1171` |
| `stop` | `3c6fdd12d20a9444c3256170405e94b7d6cf88b1aed7902e3e3aee6c0debf603` |
| `stand-1` | `004fe43c667649b0d90ef3dea9d56e193e49afdec8f6f6a6568fb56515b744d6` |
| `stand-2` | `a7baafc7dd4f75b6e05a488e2f470b1d3306027968a76a15d4d3bf0dab53b6ca` |
| `stand-3` | `7554e0b375e04bd09455c65158b43249d99c5b42fccb05b5a8451f67c3f4629f` |
| `stand-4` | `b4f328ec176347345a78df23786d682cfc3ee69591f781a1ecb5d48ee6d6642b` |
| `stand-5` | `f84cb3cd83cf8306840277badc6689d7df0f7f19f899406fadc77263067ff1ac` |
| `jump-1` | `f986d24b9b9e34ad53119824008e8ec440beb5cd0fe8293e4d90c2ec620bb330` |
| `jump-2` | `5c50e91f1e888be1db77135973e61e8da620353da147d580cd38bdeb1610b054` |
| `rise-1` | `1b977a6e7cd96246b6482be6acd436314c6e97461ef6f78cbccbfcb47d278959` |
| `rise-2` | `93fbe24fe835247cd8e2c00435f900087778037d87c73f59097e964fb3be4757` |
| `rise-3` | `688db3b9e151c4cb15204a24e07faaa8413120619ac344154921e4f0f4bb6649` |
| `rise-4` | `7bcd4889e9436b91ef51f357d493f865eac62b2dcb89f30a6c646a6051d9bc6a` |
| `rise-5` | `550a12cbb427b7bee7549b910890d8835e79cf197dcfe13bdff906a7ba9ba614` |
| `rise-6` | `6ca478f0e8bec5b89623619a0e47cf5591c65780810e21e74811975ae1197abc` |
| `rise-7` | `58aab5afa82d057d52e938bf3dc92e741e3ddac12cede4e084e1e1e75c377563` |
| `rise-8` | `76774bd65418cce9ca170556bb817f0cb2edf43d0a32d49ba06bea42f66e87c6` |
| `air-0` | `4e852e62aefbe279476a27e9c8be69d3004cb0eb510750d6d184f12533e93f15` |
| `air-1` | `c8717062eeea830c1833e6dbcc40e5e7885e44587159bb87f70821ca99818389` |
| `air-2` | `f34102792ae5842ff241f6be255feb85e6664152fb0cb5c4e1d65f281ecf973a` |
| `air-3` | `c21fcae90c91c92d62ac4e0b541d664bb7fed0af97333337aa9eb65ef0bad4c4` |
| `air-4` | `8bd607b6c7b20ae0ddad9e350af61e8ef7fc3ee6fa4d58abe70eef30f28c0cbe` |
| `air-5` | `4e50247e9c7daaef441de409c20eb3e3c5b5307899ca3a3d5f3ee27ed6b07bf4` |
| `air-6` | `3c55e75329625f75afb94b157251a0c1f8ed35aa585e15d70cf20d0450f92798` |
| `land-1` | `071bb10e9146db4cf64a46de46fbb9e125ad18025f32b16d278a81c53ed48859` |
| `land-2` | `e9c6583be091b93204aa1f68e4d5bee697d22b659432590e5fbf99b18de835f4` |
| `land-3` | `ffe44f8dc8abd3bbcb0bbc2c093ff9cb575c76f8f961e555c08918784e7c262a` |
| `bounce-1` | `f7f3875a277149592416208d80a1d24bc68fc3ceea2413bed49103ba6e95aaad` |
| `bounce-2` | `d7ab095272be3883968fbd0f50465575c56b5fa07c75cdda6498fc6afb23a819` |
| `bounce-3` | `82b10e9e5284a5d7da3511ece46a3a1052c767a488f058eda5b50b5161f264d6` |
| `bounce-4` | `2a9654187a97444654379b13ffd68a1dce8360fd8d853e5ae7650c76e250b45e` |
| `bounce-5` | `573acc69a0425780cb567326d0d78d01790f8028d81d7dab95b80dfb29cc53e8` |
| `bounce-6` | `dfd4fd3b8ca07a92e673c34cc0a2241d9673315d001f701a5203fab2acb493c8` |
| `bounce-7` | `a163baa8a900201b09e2822617500831372addfd2ec17c7f2c1fcd18cb8a63c9` |
| `bounce-8` | `47fdd0574f55acec29b9f0a0375f116fa75c763827ad42409d431e5bfcd900dd` |
| `bounce-9` | `c0544693ffe6ae05c34743f5b32e5c6cc9af49e19a83f5d3cae03af76e83d29e` |
| `bounce-10` | `63fae7d24294916456ed1789a492597fe524071d723f2219653efc48855450fe` |
| `bounce-11` | `0d51a9f292934cb73ad19bd0d3048caf1cbc6bf73f873be6a24f8a76dd1c7026` |
| `bounce-12` | `7cd131cc14f39d102ab4aea666665020317e54b9994347c3b480fc71c0352055` |
| `bounce-13` | `a23e743d7f0f325ccc808e58c4531cc5ee498f9448916ac4c4925eecff86d798` |
| `bounce-14` | `ae93828761146d17e8742278e97029f2bac74e6e486b6081509e8d78afa3856a` |
| `bounce-15` | `91bff11041ab77dcd73ec276ff502050ffac922ef4cda780eaad4585c4b5473c` |
| `bounce-16` | `e94fccbe55b920f8bb70ac1f6fab29a70e19de81c274b5c67e265f1718766519` |
| `walk-1` | `f2a4a8ea5c197c39fe642e16da3e12c7dcfa80b953f8deee62050f06b02aeba1` |
| `walk-2` | `ead13c4d2f7d70e2ac569b89777ca09fc0d96b86b23e92d69c94658ef6422594` |
| `walk-3` | `b779e9d3935e5ba3d7b394119f9f745dcfa7fd02ee1fa3d9b10f13619a5c5144` |
| `walk-4` | `b2d756f5f773372931a834bc87ad462de78bfe7c15e7789978faa9e9976b6321` |
| `walk-5` | `028edbcc0fbdbfa9c3852bcb514ea55f9c7b19f0bc65de37c01fa29f7fae548f` |
| `walk-6` | `d52593a0df652f58feeb50582fdb4d59a79264b1b02c32682a01b2f0129692ee` |
| `walk-7` | `18264b178b8cb8fafef9105eab655b6870e1d8bb162d701f4d7082b1558e607b` |
| `walk-8` | `d2bdfeebc70818c20983d7c5ce69ad37418ac3811ca09525ca15ba6146954338` |
| `walk-9` | `226119ba16a39e678c3e604efc6457e672b2758f9f121fd0efc50f9334096e2d` |
| `walk-10` | `7e855c5bc18ce4d143d64f9d2e79a74b6fba09b439cdebfe8fdb6fd94cee89df` |
| `walk-11` | `0b5255e8eab4572f97923d3f31a149333dc9090387e099b07fb368a77192cfd4` |
| `walk-12` | `c68690b3924217273e47d031decb95b0dce199f1668d70afd4814a1a8326d868` |
| `walk-13` | `df323a8b3ac8ad68b84260e7101f8035de61a5946aec230dcb0941ba4a6517ff` |
| `walk-14` | `04107531454b0c00fc1b21e5b9ce75530f53f201a1aa8a9c4efa1f75fdfbbf39` |
| `walk-15` | `b641cb7b596e5329df8d54db4f9f058b0956502579bdb2610a347b46ea5a9399` |
| `walk-16` | `ad7057061f07a2de77269eefbc01fcd1642a65b52373902d510b074392f06837` |
| `walk-17` | `5750201a322642bf0325f7c68151971ed2a686e1a4c4ca1cf617b34056535e11` |
| `walk-18` | `7a50e074c9589e17dd0ce4c18300b7aa30f5cd07b1aee36adec9a1d2f2c907c3` |
| `walk-19` | `f1c4cfcf5e3b292c4269d1a9b543059ddbdeb5cc0c507ff3bb8ae23823938e35` |
| `walk-20` | `f9f29b934568b969e6f1cc0a96cac48183701cb32fbb4edb58fc6c84a5032308` |
| `run-1` | `b6f865d897d7da52ff20a929b661ba160f1e5f85ce7c9bfc560e53f1d71679ba` |
| `run-2` | `77764f275b81969aa03223b84fa5e6f3f51380bee41a4dc52aa761b0407b9951` |
| `run-3` | `3033b61264d87a900be073693e5a8b9d29d69b1b46d01b1b699ea1e004ce4105` |
| `run-4` | `7e6bc8f655b42d82772e41834b58e54f7215578e4249e9f8b4c4cbc389b918b6` |
| `run-5` | `3dbe3765da062ab5a8cc4393799066a189fff9ba9dd82e825c63ee55f662a4f7` |
| `run-6` | `33a7cb8129e0abcfcf5e8a27d8f0abe7b58bd6a6e59dd863b8d40c8663861d08` |
| `run-7` | `b6fa595089aa760cc0d62cd45316b85ad3c0b7669cc485666fd5363246c51cfb` |
| `run-8` | `7b9bb24e9ecad1da5ec38d882bbfcc21fbb205f2bddb8237fc40ab63582980d0` |
| `run-9` | `1ad7d04ec267b66d2f0410a66041766cf1258aacdcd858843444d29f8e66b020` |
| `run-10` | `7ee72d77abb36e1e7aba2a5fbc334204a3b0fa3dc6d7f9f04befe4116b612e66` |
| `run-11` | `7f7526cf1b159a12e4813cbb8e1b73471d20edab8d1f7a0ccf3b34f98ffc7011` |
| `run-12` | `98c7c64d18dee901a4f8f7d1520f9d7636a0b644acfbed393f6887c3dbb6e408` |
| `run-13` | `785c92acb236559c7775f7ddd6bf23a28fa7b5b5720892980bba117a9c516a32` |
| `run-14` | `84887ec3e2c94a8868e1857af4873ef024fd493cc73ac8ee247da3ae2f6d3fbd` |
| `run-15` | `a0775ce709377dbd41ce05ceb6d8a953238b07283e0caf475922a7b51b026130` |
| `run-16` | `53ebca1e61285c486cae0b697a7b090dc15d7ec707a4d3f651e092384dc553eb` |
| `run-17` | `9929100a5c57b8f3199f338b085e75b7113cb99c8a6f8fcb701b57036c1c0865` |
| `run-18` | `5a0d9375b560dee536d02908e10a4da1481e75fe7b6bed231312226f5b49931e` |
| `run-19` | `02074bd88a36837ea9beefa49e9bafc435ac89e4c80c8ca55e7c58831b377224` |
| `run-20` | `3d52847919d709b7e0e53b155f1334f4f9c132278d820671b92ae0fcab0308b7` |
| `turn-a` | `2717571d7a62db0b56f4d6b4174d066e8fea0b600304053c62626dbf5df34fcd` |
| `turn-b` | `8f355022b2be6e6b2bdf5f652729186f57ef82a24c1f4e711a3b55ddc173a9a1` |

| Second character | Picture hash (SHA-256) |
|---|---|
| `s-stop-1` | `43027c100fbbd80d664ca0b03465cab3d20cb68bf20648981920e94fbb4a2cb3` |
| `s-stop-2` | `313a44cf5203b99b98ec2030abbd1deea906d15109ad6736dc01c34e9cf38b9c` |
| `s-stop-3` | `61ec973d83cb594930dbecbd50d3ea924bc198149e28dbae44e03303ea485cb8` |
| `s-stop-4` | `183c7d39b26859c048f13e80381738893ab18b8bb73c6c446a42d238f0d2b5cd` |
| `s-stop-5` | `96d312d91fd5b4aabea2271d5ad6cf667142dbb37228ef558e72f8381d0af7ba` |
| `s-stand-1` | `c96e37978ef9f66368de70e708e0cb10cc1347e24f43e9222b783defa0221c11` |
| `s-jump-1` | `171a596201c949c0f31ee4dc50eb5c9f31a63dc03a74224a47c5fdf6df13cfe3` |
| `s-jump-2` | `7a25d69f9869fcaaa12debb0752478d080e0433bf380e198a429800d45c5c8da` |
| `s-rise-1` | `dfda6a14e311f0ef690d363d68ab366ed3d6e06ab1d5dc29fcfc82df7ee11b32` |
| `s-rise-2` | `163a5ca10d16c528365e64081499817f0628afba467800b2ac07047d1c1bbeb7` |
| `s-rise-3` | `cb869e313c211297ec218d7f090e78ff7c3d40ec933f99b2f20192e02af06aaf` |
| `s-rise-4` | `afc7ac1eaccb359928fdbb8d64101a4c572acc3660b4d3a316bd42b089760eeb` |
| `s-rise-5` | `7b44b7fdc164bb573ae0473e49cccca49c10318daf51bb5dcd811800879a9fff` |
| `s-rise-6` | `24cb46f729c07967c0359c8fa7cca68651fbd4d80cf3e22199e30318fcb3ba7f` |
| `s-rise-7` | `30bc2c112829d653907735ffe1e3ffa6ad936a88bb12178d3a04cf54bc202da8` |
| `s-air-1` | `02b3162f1894ae8dca8296da5109156441253b05c0594fbc8fe740595fccdc92` |
| `s-air-2` | `b3d4cd1928d232c98fd674a3a1d41282cc23a163672bb08884c893f4ff28ffb7` |
| `s-air-3` | `002c1abdc0178ce980992efc81ee5d5eb7530b5b98ef529cb7cbb7f03b32d3db` |
| `s-air-4` | `3bcc5357806ef9872c6b29a57fdb29e65cb0ab4b82d795e4ae081305a2a449b5` |
| `s-air-5` | `63ee2b65b5f5d674d9c585ee691d1c97a47346f11951b1d37d6d9c8460d2f346` |
| `s-air-6` | `d9fc3b7253494e0351c948629104eeb530646fab813995883fbab71f00c8d5d5` |
| `s-land-1` | `53ff1b65a28a9c26fe13480d5ff8c2bea60910ae79f53b9b1a70c88e1f8c3868` |
| `s-land-2` | `6013f3677c1393724c1f882d78d014da69f25e336448303b10211f4ece31f3a2` |
| `s-land-3` | `c1c24dc0b4caa4481e47d0ae942398cf4be1664146a69e34760023f666895f89` |
| `s-land-4` | `a5850548dbd60c824d2bfc752768b90da7669043e03cb97efdedfb2abf985e97` |
| `s-bounce-1` | `aa5af774ce0f14bdff10b1c89725c4107a0c2fa5dffc10d1c4e9c91bfb0520b1` |
| `s-bounce-2` | `b40d5b5deb112862c4fbd80476cf07c51a7a34b8c2ecdab4b975905b3ce73b7b` |

**When a hash isn't found.** The importer looks for every hash in this
file (`../formats/sprites.md`, "Picture hashes"). The pictures of one
character in one of the sections below ("The stop after a roll", "A jump
and its landing", "A bounce", "Walking, running and
turning", "A roll", and the first standing pictures) belong together: if any of their hashes isn't found, the
engine shows its own art for that state on those frames, keeping the
timing given here. A character missing every hash shows the engine's own
art throughout.

These pictures don't depend on speed, on how high the jump was, or on
what is held, except where a rule below says so. While the player is
flashing (`protection.md`, "Flashing") the character isn't drawn on some
frames; on the frames it is drawn, the picture is the one given here.

## A roll

This section is for the first character only. S is the roll's
`roll_start` frame and D the frame after its last roll frame
(`roll-input.md`, "How a roll ends"). L and G are the first airborne
frame and the landing frame of a roll that goes off a ledge
(`rolling-off-ledges.md`). On the roll frames, S to D − 1, the picture
follows a **roll cycle** of 33 frames:

| Cycle frame | Picture |
|---|---|
| 1 to 3 | `roll-1` |
| 4 to 6 | `roll-2` |
| 7 to 9 | `roll-3` |
| 10 to 12 | `roll-4` |
| 13 to 15 | `roll-5` |
| 16 to 18 | `roll-6` |
| 19 to 21 | `roll-7` |
| 22 to 24 | `roll-8` |
| 25 to 27 | `roll-9` |
| 28 to 30 | `roll-end` |
| 31 to 33 | `stop` |

Cycle frame 33 is followed by cycle frame 1 again. The cycle starts
over on these frames, whatever it was showing:

- **S:** cycle frame 2, so `roll-1` shows on S and S + 1 only. Each
  later picture is shown on 3 frames, and on a roll of 29 frames
  `roll-end` shows on S + 26 to S + 28, the roll's last three frames.
- **S of a roll that starts on D + 17 of the stop after another roll**
  (the 30-frame roll of `roll-input.md`, "Starting the next roll",
  whether from a remembered press or a new one): cycle frame 1, so
  `roll-1` shows on S to S + 2 and every later picture one frame later
  than above. `roll-end` again shows on the roll's last three frames,
  S + 27 to S + 29. A roll that starts on D + 18 or later starts at
  cycle frame 2, as above.
- **L:** cycle frame 1, on the first airborne frame of a roll off a
  ledge, whether or not the player hovers there.
- **G:** cycle frame 1, on the landing frame, when the roll goes on
  after it (D = G + 30). When the player lands on L + 30, D = G and the
  stop's pictures start on G (below).

In the air the cycle goes on as long as the fall lasts: on a fall of
more than 30 frames `stop` shows on L + 30 to L + 32 and `roll-1` again
from L + 33. After a landing the roll ends on G + 29 with `roll-end` on
G + 27 to G + 29, as on a roll that started on G − 1.

A roll that is stopped (`vx` 0, `rolling-off-ledges.md`, "Stopping
before a ledge") goes on through the cycle as usual.

**A roll that defeats a walker** (`rolling-into-walkers.md`): the
picture doesn't change on the two frames of the pause, K + 1 and K + 2,
and the cycle doesn't move on: from K + 3 every picture comes two frames
later than it would have without the defeat. A new roll started by the
boost on P, while the player is still in `roll`, starts at cycle frame 2
on P, as on S. The three frames that are still `roll` after the roll
frames, before `roll_end` (D + 2 to D + 4 without a boost, P + 29 to
P + 31 after one), show `stop`, as when a roll carries on into running
(below), and walking pictures follow.

**From D:** the roll's stop or its carrying on into running, below.

**A jump out of a roll:** on the press frame P, the frame before the
jump's T (`roll-speed.md`, "Jumping out of a roll"), the player shows
`jump-1`, and then the pictures of "A jump and its landing" from T.

For example, standing on the floor with Right held, a normal roll is
pressed on S and Action is let go; the roll ends in a stop:

| Frames | Picture |
|---|---|
| S and S + 1 | `roll-1` |
| S + 2 to S + 4 | `roll-2` |
| S + 5 to S + 7 | `roll-3` |
| … 3 frames each … | … |
| S + 23 to S + 25 | `roll-9` |
| S + 26 to S + 28 | `roll-end` |
| D = S + 29 to D + 2 | `roll-end` (the stop, below) |

A roll off a ledge, measured on the raised stretch where the ground
simply ends: walking right, Right held, Action tapped on S. The player
leaves the ground on L = S + 6, hovers, and lands on G = L + 40 =
S + 46:

| Frames | Picture |
|---|---|
| S and S + 1 | `roll-1` |
| S + 2 to S + 4 | `roll-2` |
| S + 5 | `roll-3` |
| L = S + 6 to S + 8 | `roll-1` |
| S + 9 to S + 32, 3 frames each | `roll-2` to `roll-9` |
| S + 33 to S + 35 | `roll-end` |
| S + 36 to S + 38 | `stop` |
| S + 39 to S + 41 | `roll-1` |
| S + 42 to S + 44 | `roll-2` |
| S + 45 | `roll-3` |
| G = S + 46 to S + 48 | `roll-1` |
| S + 49 to S + 72, 3 frames each | `roll-2` to `roll-9` |
| S + 73 to S + 75 | `roll-end` |

D = G + 30 = S + 76, and the stop follows.

## The stop after a roll

D is the frame after the roll's last frame (`roll-input.md`, "How a roll
ends"; for the second character the roll lasts 24 frames,
`second-character.md`, "Rolls").

**The roll ends in a stop.** The first character:

| Frames | Picture |
|---|---|
| D to D + 2 | `roll-end` (the roll's last picture, already shown from D − 3) |
| D + 3 to D + 8 | `stop` |
| D + 9 to D + 16 | `stand-1` |

The second character:

| Frames | Picture |
|---|---|
| D to D + 4 | `s-stop-1` |
| D + 5 to D + 10 | `s-stop-2` |
| D + 11 and D + 12 | `s-stop-3` |
| D + 13 and D + 14 | `s-stop-4` |
| D + 15 and D + 16 | `s-stop-5` |

So the picture does change during the stop: three times for the first
character, five times for the second. The same pictures follow a slow,
normal or fast roll, whatever is held during the stop.

When the player is standing on D + 17, the standing pictures start
there: for the first character `stand-1` × 3, `stand-2` × 3, `stand-3`
× 3, `stand-4` × 3, `stand-5` × 3 from D + 17 (so `stand-1` is shown
from D + 9 to D + 19); for the second character `s-stand-1` × 4 from
D + 17. The standing pictures after these are not covered.

A jump or a roll that starts during the stop, or on D + 17, ends these
pictures: a jump shows `jump-1` on the frame before its T and then the
pictures of "A jump and its landing"; a roll shows roll pictures from
its first frame.

**The roll carries on into running.** The first character shows `stop`
on D, D + 1 and D + 2, and the second character `s-stop-1` × 3 then
`s-stop-2` × 3 from D (D to D + 5). For the first character, the
walking pictures of "Walking, running and turning" follow; for the
second, what follows is not covered.

## A jump and its landing

### First character

T is the frame carrying `jump_start` (`jump-arc.md`). A, the stage
frames, L, M and n are as in `jump-input.md` ("The switch frame M"), and
K, the frame walking, running or standing starts, is as in
`ground-speed.md` ("After a landing").

The pictures below are the same for the first character's jumps out of a
roll, pressed on the ground or in the air (`rolling-off-ledges.md`,
"Jumping during the fall", hover frames included): T − 1 is the press
frame P, which shows `jump-1` ("A roll"), and
A, n, M and K are found as for any other jump, so the air pictures, the
landing pictures and the standing pictures from K follow the same rules.

**In the air:**

- T − 1: `jump-1`. T and T + 1: `jump-2`. Then `rise-1`, `rise-2`, …,
  `rise-8` for 2 frames each (T + 2 to T + 17). From T + 18: `air-0`.
- From A on, on each frame `t` until the landing, the picture is
  `air-`*s*, where *s* is the number of stage frames that are `t` or
  earlier, at most 6. So `air-0` is shown up to A − 1, `air-1` from A,
  and `air-6` from the sixth stage frame until the player lands.

**The landing frame L** (when M is later than L): the same rule as in the
air, `air-`*s* for `t` = L.

**From L + 1 to M − 1** (only when M is L + 2 or later): on L + *j*, the
picture is `air-`*m* with *m* = min(6, n + 2*j*). So the air pictures
still to come are passed two at a time, and M − 1 always shows `air-6`.

**From M to K − 1**, when running doesn't start on M: `land-1`, then
`land-2`, then `land-3`, ending with `land-3` on the three frames
before K:

| K − M | Pictures from M to K − 1 |
|---|---|
| 6 (M later than L) | `land-1` × 1, `land-2` × 2, `land-3` × 3 |
| 9 (M = L, L later than the seventh stage frame) | `land-1` × 3, `land-2` × 3, `land-3` × 3 |
| 10 (M = L, L the seventh stage frame) | `land-1` × 4, `land-2` × 3, `land-3` × 3 |

When running starts on M (`ground-speed.md`, "After a landing"), the
player shows running pictures from M, and `land-1` to `land-3` are not
shown.

**From K:** when the player is standing on K (no direction held, as in
the question this answers), the standing pictures start as after a
roll: `stand-1` × 3, `stand-2` × 3, `stand-3` × 3, `stand-4` × 3,
`stand-5` × 3. When walking or running starts on K, its pictures start
there (not covered).

Example: a standing jump on flat ground with Jump pressed on T − 1 and
held 1 frame (A = T + 20, L = T + 31, n = 6, M = L + 1, K = L + 7, as
in `jump-input.md`). `air-0` on T + 18 and T + 19, `air-1` to `air-4`
for 2 frames each from T + 20, `air-5` on T + 28 to T + 30, `air-6` on
L (the sixth stage frame), `land-1` on L + 1, `land-2` on L + 2 and
L + 3, `land-3` on L + 4 to L + 6, and `stand-1` from L + 7.

Example with M = L: Jump held 12 frames, A = T + 21, so the stage
frames are T + 21, T + 22, T + 24, T + 26, T + 28, T + 31 and T + 35.
`air-0` on T + 18 to T + 20, `air-1` on T + 21, `air-2` on T + 22 and
T + 23, … `air-5` on T + 28 to T + 30, `air-6` from T + 31. Landing on
L = T + 37, later than the seventh stage frame: M = L, K = L + 9.
`land-1` on L to L + 2, `land-2` on L + 3 to L + 5, `land-3` on L + 6
to L + 8, `stand-1` from L + 9.

### Second character

In the air it shows `s-jump-1` on T − 1, `s-jump-2` on T and T + 1, then
`s-rise-1` to `s-rise-7` for 2 frames each (T + 2 to T + 15), then
`s-air-1` and `s-air-2` to `s-air-6`. When `s-air-1` ends is not covered
here. `s-air-6` is the last picture of the air; the player shows it
until landing.
A jump out of a roll pressed on the ground (`roll-speed.md`, "Jumping
out of a roll") shows the same pictures, with T − 1 the press frame. So
does a jump out of a roll pressed in the air, during the fall after
rolling off a ledge (`rolling-off-ledges.md`, "The second character",
hover frames included), with T − 1 the press frame P.

After a landing on L that comes on the fourth or a later frame showing
`s-air-6` (every jump measured that lands on the floor it took off from
is such a landing):

| Frames | Picture |
|---|---|
| L | `s-land-1` |
| L + 1 and L + 2 | `s-land-2` |
| L + 3 to L + 5 | `s-land-3` |
| L + 6 to L + 13 | `s-land-4` |
| L + 14 to L + 16 | `s-land-3` |

From L + 17 the player is standing, walking or running, and shows its
pictures (standing: `s-stand-1` × 4 first). If Action and exactly one of
Left or Right are held on L, the player runs from L instead and shows
running pictures from L, with none of the pictures above. This is the
same after plain jumps and after jumps out of a roll, pressed on the
ground or in the air (`ground-speed.md`, "After a landing").

## A bounce

B is the bounce frame (`bounce.md`); for the second character, whose
bounces `second-character.md` doesn't cover yet, B is its first
bouncing frame, and its speeds also stay the same on B + 1 and B + 2.
The picture shown on B is also shown on the two frozen frames B + 1 and
B + 2, and on B + 3: the frozen frames add two frames to it. The next
picture comes on B + 4.

| Character | B to B + 3 | B + 4 |
|---|---|---|
| First | `bounce-1` | `bounce-2` |
| Second | `s-bounce-1` | `s-bounce-2` |

### The first character, from B + 4 to the landing

From B + 4 the pictures go on in order, each for 2 frames: `bounce-2`
on B + 4 and B + 5, `bounce-3` on B + 6 and B + 7, and so on to
`bounce-16` on B + 32 and B + 33. Then they start again from
`bounce-1`, now also for 2 frames (B + 34 and B + 35), and go round the
16 pictures `bounce-1` to `bounce-16`, 2 frames each, for as long as the
player is in the air:

| Frame | Picture |
|---|---|
| B to B + 3 | `bounce-1` |
| B + 4 + 2*i*, B + 5 + 2*i* (*i* = 0 to 14) | `bounce-`(*i* + 2) |
| B + 34 + 2*j*, B + 35 + 2*j* (*j* = 0, 1, 2, …) | `bounce-`(*j* mod 16 + 1) |

Speed, height, Jump and direction input don't change them. A second
bounce before landing (`bounce.md`) is a new B: `bounce-1` shows from
it for 4 frames and the order starts again.

**The landing frame G** (the frame carrying `land`): there are no
landing pictures. The mode that starts on G (`ground-speed.md`, "After
a fall or a bounce") shows its own pictures from G:

- running: `run-1` on G, then as in "Walking, running and turning";
- walking: `walk-1` on G, then as there;
- a turn: the turn's pictures from G, as there (`turn-a` on G and
  G + 1, …);
- standing: `stand-1` × 3, `stand-2` × 3, `stand-3` × 3, `stand-4` × 3,
  `stand-5` × 3 from G, as after a roll's stop. The pictures after these
  aren't covered.

A roll pressed on G starts on G + 1 (`ground-speed.md`); its pictures
are in "A roll".

Example: a bounce with Right held, landing on G = B + 41 with Right
still held. `bounce-1` on B to B + 3, `bounce-2` on B + 4 and B + 5, …
`bounce-16` on B + 32 and B + 33, `bounce-1` on B + 34 and B + 35,
`bounce-2` on B + 36 and B + 37, `bounce-3` on B + 38 and B + 39,
`bounce-4` on B + 40, and `walk-1` on G.

The second character's pictures after B + 4 are not covered.

## Walking, running and turning

This section is for the first character only. While it is walking,
running or turning on the ground (`ground-speed.md`, "Standing, walking
and running"), or in the air after walking or running off a ledge
without a face, where the mode goes on (`ground-speed.md`, "After a fall
or a bounce"), its picture follows the stride cycle and the turn:

- **Walking**, on a frame in step `s` of the stride cycle (1 to 20):
  `walk-s`, so `walk-1` in step 1, `walk-2` in step 2, and so on.
- **Running**, in step `s`: `run-s`.
- **Turning**: `turn-a` on the turn's first two frames (T and T + 1),
  `turn-b` on T + 2 to T + 5 and `turn-a` on T + 6 and T + 7. T is the
  frame the turn starts on (`ground-speed.md`, "Facing and turns"). Facing, and so
  the mirroring, changes on T + 4, in the middle of the `turn-b`
  frames.

The step is the one the frame ends in: on a frame where a step ends,
the next step's picture is shown, and on the frame walking or running
starts, the step 1 picture. A picture is shown for as many frames as
its step lasts, so at low speeds most pictures are shown on two or
three frames in a row, and at higher speeds some on one frame only.
Nothing else changes the picture: it doesn't depend on what is held or
on whether the player is speeding up or slowing down.

Leaving the ground at a ledge without a face changes nothing either.
On every frame of the fall the picture follows the same rules as on the
ground, from the step or turn frame reached at the edge: the stride
cycle's steps go on and are shown, a turn started in the air shows the
turn pictures (mirroring changes on T + 4 in the air as on the ground),
running started in the air shows `run-1` on its first frame, and the
landing frame G shows the picture of the step or turn frame it is in.
No air or landing pictures are shown.

When walking, running or a turn ends in standing (`ground-speed.md`,
"When a step ends" and "Facing and turns"), on the ground or in the air
after a ledge without a face, the standing pictures start on the first
standing frame: `stand-1` × 3, `stand-2` × 3, `stand-3` × 3, `stand-4`
× 3, `stand-5` × 3, as after a roll's stop. The pictures after these are
not covered.

For example, the first character standing still on flat floor, with
Right held from frame 0 on. Walking starts on frame 0; v (|`vx`| after
that frame's speed rule, `ground-speed.md`) and the counter after each
frame are:

| Frame | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| v | 64 | 120 | 169 | 211 | 248 | 281 | 309 | 334 | 356 | 375 | 392 | 407 | 420 | 431 | 441 | 449 | 456 | 463 | 469 |
| Counter | 256 | 160 | 64 | 224 | 128 | 32 | 192 | 96 | 251 | 144 | 29 | 166 | 41 | 169 | 36 | 156 | 17 | 133 | 246 |
| Step | 1 | 1 | 1 | 2 | 2 | 2 | 3 | 3 | 4 | 4 | 4 | 5 | 5 | 6 | 6 | 7 | 7 | 8 | 9 |

So `walk-1` shows on frames 0 to 2, `walk-2` on 3 to 5, `walk-3` on 6
and 7, `walk-4` on 8 to 10, `walk-5` on 11 and 12, `walk-6` on 13 and
14, `walk-7` on 15 and 16, `walk-8` on 17 only and `walk-9` from 18.

The frames between a roll carried on into walking and the step 1
picture are in "The stop after a roll". A walk or run that starts at
the end of a landing (`landing.md`) starts at step 1 with `walk-1` or
`run-1` in the same way.

## Evidence

Measured in `title-1` by watching the screen. On every logic frame of
each run, the screen was rendered with the sprite layer alone, and the
picture at the player's drawn origin (`../formats/sprites.md`, "Where
the picture is drawn") was compared with every full frame's picture
there, decoded and mirrored as `sprites.md` says. A picture matched when
every opaque pixel showed a colour other than the background, each colour
number showed a single screen colour, and the transparent pixels of its
box showed background. The largest matching picture was taken. When
another sprite crossed the box, its transparent pixels weren't checked.
When another sprite covered part of the picture, a picture already seen
in the run was taken if at least 85 % of its opaque pixels agreed and no
other came within 10 %. Frames where no picture matched (mostly another
character or an enemy in front of the player) were left out and are
counted below. The picture is named by its hash, and D, L, M, K and B
are from the trace.

The runs were scripted cases on flat floor and runs of random input,
with the lead kept from being hurt: 458 098 logic frames, of which
40 707 had no matching picture. Each case was followed until the player
left the ground or did something else (a jump, a roll, a slide on a
steep face, picking something up):

- **Stops after rolls:** 731 with the first character and 682 with the
  second, after slow, normal and fast rolls with random inputs through
  the stop (nothing, either direction, Action, Up, Down and Jump, alone
  and together), 638 and 644 of them followed to D + 16 (11 676 and
  11 389 frames compared, 10 left out). 0 differences. Carried-on
  rolls: 258 and 251, 0 differences.
- **Landings, first character:** 992 landings after standing, walking
  and running jumps: 186 on flat floor with Jump held 1 to 40 frames
  and 806 in random play through the first level, on the floors met
  along the way (5 945 frames compared, 59 left out). n ranged from 3
  to 7, and M from L to L + 3. Every picture matched. In one more jump,
  `jump-1` was still on screen on T and `jump-2` came on T + 1 only.
  Landings whose take-off picture wasn't seen, or where the game paused
  with the player in the air, weren't compared.
- **Landings, second character:** 373 landings (188 on flat floor with
  Jump held 1 to 40 frames, 185 in random play), 53 of them running from
  L (4 380 frames), 0 differences.
- **Landings after jumps out of a roll, second character:** 800 runs
  on flat floor, 398 of them jumping out of slow, normal and fast rolls
  (Jump pressed on a roll frame from the roll's first frame to 23 frames
  after it, held 1 to 40 frames) and the others with plain jumps, with
  random input in the air and after the landing (which started more
  rolls and jumps), and random play through the first level from two
  places. From the press frame to T + 15: 617 jumps out of a roll on
  flat floor, 0 differences. Landings on the fourth or a later `s-air-6`
  frame, compared from L up to the first standing, walking, running or
  `s-jump-1` picture: 420 after jumps out of a roll (367 on flat floor,
  53 in random play; 2 094 frames), 0 differences, 144 of them running
  from L. In the same runs, 751 landings after jumps from the floor
  (3 547 frames) matched too.
- **Jumps out of a roll pressed in the air, second character:** 1 600
  runs with the second character leading on the three ledges of
  `rolling-off-ledges.md` ("The second character"): walking right 0 to
  36 frames, a normal or slow roll (Action tapped or held), Jump pressed
  on L to L + 20 (about a quarter of the presses during the hover) and
  held 1 to 40 frames, random input in the air, and random input
  changed every 1 to 8 frames for 60 frames after the landing. Runs
  that fell into a pit were cut there. From the press frame to T + 15,
  and `s-air-6` from its first frame to the landing: 363 jumps, 0
  differences; 343 more had a frame where the player's picture was not
  wholly on screen and were left out. Landings on the fourth or a later
  `s-air-6` frame, compared from L up to the first standing, walking,
  running or `s-jump-1` picture: 92 (452 frames), 0 differences, 35 of
  them running from L.
- **Bounces:** 72 bounces off enemies with the first character leading
  and 21 with the second (295 and 61 frames compared, 65 and 44 left
  out, mostly the enemy in front of the player). All 21 of the second
  character's bounces and 71 of the first's matched. In the other, the
  picture from before the bounce was still on screen on B, and
  `bounce-1` showed from B + 1 to B + 3.
- **After bounces, first character:** 1 487 runs bouncing off the first
  level's first walker, from jumps held 1 to 40 frames, with random
  input in the air (either direction, Action, and Jump held from B + 3
  for 0 to 60 frames or throughout) until 30 frames after the landing;
  587 bounces, 106 of them second bounces. In 600 of the runs (201
  bounces, in the air for up to 56 frames, so the second round seen from
  `bounce-1` to `bounce-11`): 9 373 frames in the air compared, 27 left
  out, 0 differences. In the other runs the player was raised 80 to 240
  px a few frames after the bounce so that the fall lasted up to 85
  frames, and the picture was often partly or wholly above the screen:
  of 21 414 frames in the air, 7 461 had nothing drawn and 1 619 matched
  no picture or another object's; 12 334 showed a bounce picture (each of
  the 16 also seen from B + 34 on), and all were the picture given above
  except 4, where the previous picture was still on screen for one frame
  (3 of them on the first frame of a second bounce). On G, 432 landings
  were compared (140 standing, 155 running, 81 walking and 56 turning
  from G), with the standing pictures followed to G + 14 and the turn
  pictures to the end of the turn (1 000 and 321 frames): 0 differences.
  In the random play through the first level above, 40 bounces were seen
  (950 frames in the air): one picture came a frame late, the rest
  matched.
- **Walking, running and turning, first character:** 240 runs of random
  input on flat floor (Left, Right or nothing for 1 to 40 frames, half
  of the runs with Action held throughout), 146 604 logic frames. From
  each run's first standing frame on, the mode, step and turn frame were
  worked out from the inputs and `vx` alone with the rules of
  `ground-speed.md`, and the picture they give was compared with the
  screen: 46 634 walking, 35 718 running and 21 464 turning frames
  (2 690 turns), 0 differences. Three rolls carried on into walking
  showed `walk-1` from the first walking frame on. In random play
  through the first level (28 714 logic frames, on the slopes and floors
  met along the way, steps not worked out), every grounded walking,
  running or turning frame with a matching picture showed a picture of
  that kind (3 582 frames), except 4 frames where the previous step's
  picture was still on screen, and 12 frames in a row in one run where
  the match took the picture of another object crossing the player.
- **Walking off a ledge without a face, first character:** 800 runs of
  random input on a platform without faces at either end, with a lower
  floor on one side and a pit on the other (Right, Left or nothing for 1
  to 30 frames, Action held or let go at about a quarter of the changes,
  the same input going on in the air; each run to 40 frames after
  landing or until it fell into the pit), 90 583 logic frames. The mode,
  step and turn frame were worked out from the inputs and `vx` alone as
  above, on airborne frames as on grounded ones, from each run's first
  frame and again from the next standing frame after a roll. In the air,
  in 226 falls, 4 205 walking, 1 143 running, 771 turning (106 turns
  started in the air) and 64 standing frames were compared, and 79 runs
  started in the air; on the floors before and after the falls, 16 199
  walking, 2 149 running, 3 057 turning and 607 standing frames: 0
  differences. 300 frames were left out where no picture matched: the
  player was behind the platform's front, below its top. On the 240
  flat-floor runs above, the standing pictures from the first standing
  frame after walking, running or a turn (15 897 frames) also showed
  no differences.

- **Rolls, first character:** 1 739 rolls on flat floor from standing,
  walking and running starts (slow, normal and fast), with random
  inputs from 20 frames before the roll's end (nothing, either
  direction, Action, Up, Down and Jump, alone and together), so that
  many rolls were followed by a roll from a remembered press or a jump
  out of the roll; 456 rolls in scripted pairs, the second pressed once
  on D − 2 to D + 17 of the first's stop (228 of them 30-frame rolls
  started on D + 17); 192 rolls toward the two ends of the raised
  stretch whose ground simply ends and toward two ledges with a face at
  the top, from many distances, 64 of them leaving the ground (with and
  without a hover) and 42 landing during the roll; 305 rolls into a row
  of small walkers on the first level's opening floor, with 234 defeats
  and 195 boosts; and 172 rolls in random play through the first level,
  on its slopes and floors. 65 529 roll frames were predicted from the
  trace alone (S, L, G, the stop before S, the pause frames and Action
  for the boost) and compared with the screen, 2 653 left out: 0
  differences, except 7 rolls in random play: one already under way when
  the run began, one held by a game pause (`hidden-objects.md`), and 5
  where the picture was held or started over as after a defeat, in runs
  that didn't record what the roll met. 2 094 of the frames compared
  were frames of stopped rolls. After the defeats, all 105 endings
  carried on into running that weren't hidden showed `stop` on the three
  frames before `roll_end` and `walk-1` on the next. On all 967 jumps
  out of a roll, the press frame showed `jump-1`.
- **Jumps out of a roll, first character:** 716 jumps pressed on the
  ground during slow, normal and fast rolls: 625 on flat floor (pressed
  on a roll frame from S to S + 28, Jump held 1 to 40 frames, random
  input in the air and after the landing) and 91 in random play through
  the first level. The air pictures from P matched the rule on all of
  them except 4 in random play that bounced off a walker in the air and
  then showed the pictures of "A bounce". 557 of the landings (487 and
  70) were followed until the player did something else, with n from 1
  to 7 and M from L to L + 4: 2 716 frames from L compared, 0
  differences. 71 of them were followed to K + 2 standing, and on 83
  running started on M.
- **Jumps out of a roll pressed in the air, first character:** 549
  jumps pressed during the fall after rolling off either end of a raised
  platform whose ground simply ends (normal and slow rolls, Jump pressed
  on L to L + 20, 266 of them on a hover frame, held 1 to 40 frames,
  random input in the air and after the landing). The air pictures from
  P to the landing matched on all of them (26 landed before A). These
  jumps often rise above the top of the screen; frames where the
  player's picture was not wholly on screen were left out. 154 landings
  whose last air frames were on screen were followed until the player
  did something else, with n of 1, 5 or 7 and M − L of 0, 2 or 4: 700
  frames from L compared, 0 differences, 5 of them followed to K + 2
  standing and 5 to running on M.

## Not covered

- The first character landing before the approach frame (L < A,
  M = A + 4) on plain floor: no such landing was measured. Landings
  with n = 1 or 2 (M = L + 3 or L + 4 with one or two stage frames
  passed) were not measured either; the rule above is the spec's choice
  for them.
- Why, in two of the cases in "Evidence", the new picture came one
  frame late on screen. The rules above give the usual frame.
- The second character landing before the fourth frame showing
  `s-air-6`, and when its `s-air-1` ends.
- The second character's landings after falls that didn't start with a
  jump, and after bounces.
- The standing pictures after the first ones given above, the second
  character's walking, running and turning pictures (its stride cycle
  isn't in `ground-speed.md`), its roll pictures and its pictures after
  B + 4 of a bounce, and the pictures of every other state, such as pushing
  against a wall or a steep face.
- Why, on 5 frames after bounces, the previous picture was still on
  screen for one frame. The rules above give the usual frame.
- Why, on 4 frames in random play, a walking or running picture came
  one frame late. The rules above give the usual frame.
- The first character's pictures when a bounce ends without a landing
  (catching a rope, being hurt, or a game pause in the air).
- The first character's pictures on a roll started by a boost in the
  stop after a roll (`rolling-into-walkers.md`, "The boost", P in the
  stop): the few seen were hidden behind walkers.
