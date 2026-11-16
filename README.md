# No Sweat Fixes — Dev Log

**2026 Epic AI MegaJam · Unreal Engine 5.8 · Team Floppanas**

The game and its gameplay video are on our submission page for the jam.

![Six moments from the build: main menu, shop, a short circuit, burst pipes, the attic circuits and the ghost](img/00-cover.jpg)

No Sweat Fixes is a co-op repair game for 2 to 4 players. You are a crew of handymen sent into a house where everything fails at once. Open a valve too fast and a pipe bursts. Let the puddle reach a bare wire and the water is live. Swap a fuse with the power on and you burn the spare and yourself. And a ghost stalks the house, more aggressive with every repair you finish and every word you say on the radio. The crew shares one wallet, buys supplies in the lobby, votes on the job at a blueprint board, and is paid for what it actually fixes.

We read the jam theme as free direction: the director is whoever is making the game, so this is our cut. Four people directed a crew of AI agents, kept what felt right when we played it, and cut what did not, such as the automatic camera zoom, the 3D characters in the menu and a misleading prompt on the bare cable.

This page is the English edition of the build diary we kept during the jam. The raw diary was written in Spanish, our working language, while we built: 50 entries, 361 screenshots, 212 machine-written JSON reports and 29 logs. Captures and reports were taken as we built. Every claim below comes from one of those entries or from the commit history.

## Four people, four days, and an engine we barely knew

There are four of us, and most of us have full-time jobs. When the jam opened we had practically no idea how to use Unreal Engine 5. We had ideas for a game, less than four days, and whatever hours were left after work.

The commit history shows where those hours came from. Thursday and Friday were working days, and 50 of our 163 commits landed between midnight and 7 AM our time.

The jam allowed AI as a support tool, and we leaned on it to learn the engine as we went. We found out quickly that asking an AI for help is one thing and getting everything to work the way you want is another. Following its instructions was not always enough, and more than once the answer it gave us was wrong. Each time, one of us had to play the game, notice what was off and decide what to do. The section "What broke" is the record of those moments.

What we take away is simple. AI is a very useful tool when you are learning or short on time. It does not always have the right answer, and in the end we are the ones who have to understand what we are doing, test it and make the decisions. Whatever the result, we keep everything we learned in these four days.

No Sweat Fixes is what happens when four people learn, experiment and decide until an idea becomes a game that works.

## The jam in numbers

| | |
|---|---|
| Jam window | Thu Nov 12, 00:00 EST to Sun Nov 15, 20:59 EST |
| First commit | Thu Nov 12, 00:13 EST, 13 minutes after the jam opened |
| Last commit before the jam closed | Sun Nov 15, 20:00 EST |
| Commits during the jam | 163 (21, 34, 38 and 70 per day, Thursday to Sunday) |
| Commits made between midnight and 7 AM, our local time | 50 |
| Pull requests merged | 5 |
| People | 4 |
| C++ in the project's plugins | about 17,000 lines |
| Named automation tests (`NoSweat.*`) | about 40 |
| Editor automation scripts, mostly Python | about 200 |
| Diary entries / screenshots / JSON reports | 50 / 361 / 212 |

## The team

Four people. Areas are taken from the commit history.

| Who | Commits | Main areas |
|---|---|---|
| **Jose Islas** (`BlackWolf-117S`) | 45 | Repository and integration (the 5 pull requests and the merge recoveries), the `NoSweatMultiplayer` plugin (lobby, online sessions, profiles, reconnection), mission table and voting, pipe and electricity mechanics, tutorial map, the ghost and its move into the Mansion |
| **Alejandro Segura** (`alexby117`) | 49 | Online lobby level (models, lighting, set dressing), animated main menu, vote board, illustrated shop and HUD, solo start, the character wardrobe, Mansion as the playable mission |
| **Atzin Domínguez** (`CosmoSka`) | 39 | Project and online configuration, main menu art and the new main menu map, the Shark character, crew animation retargeting, lobby tool interaction, all audio, light fixtures, credits, proximity voice chat and radio, microphone selection, settings menu |
| **Emilio Alvarez** (`milicolobrii-lab`) | 30 | The Mansion level: structure, materials, basement, laundry and attic; the ghost mesh; graffiti and mansion test scripts |

Commit counts undercount level and art work. A map is one binary file no matter how many hours went into it.

## How we worked with AI

The loop was always the same: a person asks for something in plain language, an agent implements it inside Unreal, a person plays it and says what feels wrong, and the agent fixes it. Then both sides write down what happened.

| Tool | What it did | What stayed human |
|---|---|---|
| **ChatGPT and Codex** (OpenAI) | C++ gameplay and network code, level and Blueprint configuration, automation tests, editor scripts, prompt clean-up, and the diary itself | Every design decision, all play-testing, judging whether something felt right, merges |
| **Unreal MCP** (`ModelContextProtocol` and `AllToolsets` plugins, UE 5.8) | Let the agent inspect and edit the running editor: place actors, set properties, save and reload maps, take captures | Deciding who may touch which map |
| **Meshy** (7.1 Flagship image-to-3D, Meshy 7 texturing at PBR 2K) | 14 electrical props and 42 PBR textures generated from images alone, plus the rigged ghost | Choosing references and catching wrong results |
| **fal** | 3D model generation | — |
| **ChatGPT image generation** | Character style exploration, prop reference sheets, a menu background without characters, a 2D crew layer for the menu, and transparent cut-outs for 12 shop icons | Art direction and choosing what to keep |
| **Procedural meshes written as Python by Codex** | A Phillips screwdriver (736 triangles) and a healthy fuse (532 triangles) | — |

![A typical loop: the request, the agent's report after 12 minutes 41 seconds of work, and the result in the editor](img/32-agent-report.jpg)

*A typical loop. The agent reports what it changed and that four tests passed; the result is on the right, in the editor.*

We ran several agent sessions at once, so we gave them written rules (they live in `AGENTS.md` and a project skill file in the repository):

1. Before changing an important feature, record what was asked and take a real capture of the previous state.
2. Afterwards, take a comparable capture, write a short summary, and list only the tests that actually ran.
3. If a capture or a test cannot be obtained, say so. Never present a later or generated image as the "before".
4. Failed attempts are kept, never overwritten.
5. One coordinator session owns shared source files, builds and integration. Each map or shared Blueprint has a single owner. A separate documentation session consolidates the entries.

The fifth rule matters most with Unreal maps (see "What broke").

## Day by day

All times on this page are US Eastern (EST, UTC−5), the jam's own clock. We live in Mexico City, one hour behind.

**Day 1 — Thursday, Nov 12 (21 commits).** The repository went up at 00:13, thirteen minutes after the jam opened. By 04:49 there was a main menu and a prototype co-op lobby. In the morning the `NoSweatMultiplayer` plugin landed with its lobby subsystem and UI (PR #1), followed by LAN test scripts. During the day: menu art, a new main menu map, the Shark character, the lobby board, a camera toggle. At 22:51 the mission table and multiplayer map voting were in (PR #2).

**Day 2 — Friday, Nov 13 (34 commits).** The day opened at 00:46 with online sessions through Epic Online Services, player profiles and reconnection (PR #3). From 01:33 to 08:10 the lobby was dressed overnight and the first Mansion walls went in. In the afternoon, tool interaction in the lobby. At 20:20, pipe mechanics and a reusable pick-up tool (PR #4). At 23:52, electricity mechanics with multiplayer tests. At 23:57, animation retargeting for the crew.

**Day 3 — Saturday, Nov 14 (38 commits).** Another night shift from 02:13 to 08:15: lobby lamps and lighting, Mansion textures, the basement. Between 05:03 and 05:33, four recovery merges to rescue the Mansion map. During the day, diary entries 001 to 009: interaction outline, missions and shop, Meshy models, electricity, manual repair, pipes, overpressure, linked lights. At 18:54 the Job House map. At 21:57 the animated main menu with credits (PR #5).

**Day 4 — Sunday, Nov 15 (70 commits).** From 00:20, audio. Through the night until 06:17: light fixtures, the Mansion attic, the first working circuits inside Mansion, the ghost mesh. At 10:55 Mansion became the playable mission, with a "coming soon" card beside it. Then solo start, the tutorial map, the loading transition, the ghost actor at 15:11, a compile fix at 15:19, the illustrated shop and HUD, audio credits, and at 17:44 proximity voice chat, radio and the settings menu. In the last three hours: microphone selection, a wardrobe in the lobby to pick your character, the ghost moved into the Mansion at 18:27, and a final pass on the lobby and the mission economy at 20:00, 59 minutes before the deadline.

## Where it started

On Friday at 23:39, minutes before the electricity commit, we recorded a three-minute test. Grey boxes, floating text where the UI would go, and a fox in a hard hat. Every system in the final game is already here in its roughest form: the main breaker, the valve, the pipe that bursts, the bare cable, the puddle that turns live, and the wiring.

| A wall of labels: breaker, valve, test pipe, exposed cable | Valve at 100%: the pipe bursts and the puddle goes live | Wiring as coloured blocks to match |
|---|---|---|
| ![Grey-box test wall](img/33-greybox-wall.jpg) | ![Burst pipe and live water in the grey-box test](img/34-greybox-live-water.jpg) | ![Wiring as coloured blocks](img/35-greybox-wiring.jpg) |

Two things changed once people played it. The valve keys were swapped: here `R` opens fast and `F` closes, while in the final game `F` slams the valve open and `R` closes it. And wiring went from matching coloured blocks to dragging real wire tips inside a modelled switch box. The rest of this page is how the grey boxes became a game in two more days.

## Finding the look

**Four styles, one crew.** Before modelling anything we asked ChatGPT for the same handyman in four 3D styles, each on a different animal: low poly retro on a penguin, toony on a shark, basic shapes on a giraffe, and a semi-stylized hero on a kangaroo. We chose low poly retro and had the whole crew redrawn in it.

| The style sheet, with its original Spanish labels | The crew in the style we chose |
|---|---|
| ![Four character styles: low poly retro penguin, toony shark, basic-shapes giraffe, semi-stylized kangaroo](img/36-style-exploration.jpg) | ![Penguin, shark, giraffe and kangaroo in low poly retro](img/37-crew-low-poly.jpg) |

**Props: picture first, model second.** Every prop in the lobby went through the same three steps.

1. **Reference.** ChatGPT drew each object in low poly retro, on a white background, in the game's colours.

   ![Prop references generated in ChatGPT: desk, table, toolboxes, saw, hard hat, spray can, solvent, level, box, pegboard](img/40-prop-references.jpg)

2. **Model.** Each reference went into Meshy's image-to-3D agent with one fixed prompt, the same for every object:

   > Create a clean, game-ready 3D model based strictly on the provided reference image. Preserve the original object's shape, proportions, silhouette, colors, and visual identity. Art style: Stylized Low Poly Retro, with simple geometric forms, flat-shaded polygonal surfaces, crisp edges, and minimal geometric complexity. Use simplified, optimized geometry suitable for a stylized 3D video game in Unreal Engine 5.8.4. Maintain the original color palette, including industrial blue, purple-gray metallic surfaces, yellow safety accents, orange details, and red elements where present in the reference. Avoid photorealism, realistic wear, excessive surface details, smooth organic shapes, and unnecessary subdivisions. Create a complete three-dimensional object with consistent geometry on all sides, including surfaces not visible in the reference image. The object must be isolated, with no background, floor, environment, additional props, or decorative elements. Preserve clearly recognizable functional parts. Use clean topology, correctly oriented normals, and separate materials where appropriate. The result should look like a production-ready low-poly asset from a stylized cartoon industrial game. Do not redesign or reinterpret the reference image. Prioritize visual accuracy and simplicity.

   The prompt is strict on purpose: it tells the model not to redesign the reference. Each object came back twice, textured and as a bare mesh. The crate in this screenshot is 7,953 triangles with a 2K base colour texture.

   ![Meshy's 3D generation agent: our prompt on the left, textured and untextured results on the right](img/41-3d-generation.jpg)

3. **Level.** A person placed the models in the level, lit it and dressed it.

| The crew lobby, first pass | The crew lobby in the final build |
|---|---|
| ![Grey-box lobby with a chalkboard, two tables and four orange spawn pads](img/38-lobby-greybox.jpg) | ![Final lobby with the Crew Dispatch board, shelves, workbench and lamps](img/39-lobby-final.jpg) |

It is the same room with the same job: a board to gather the crew and a table to pick the mission. The board frame, shelves, lamps, toolboxes, drill, vise and plant are the generated props from the step above.

## Building the Mansion

The Mansion is the job the crew is sent to. It was built in four stages over the four days.

| 1. A floor plan drawn with boxes (Thursday, 21:49) | 2. Volume: floors and roofs, saved as the Mansion map |
|---|---|
| ![Mansion floor plan made of box walls in an untitled map](img/42-mansion-floorplan.jpg) | ![Mansion blockout with roofs](img/43-mansion-blockout.jpg) |

| 3. Materials: brick, tile and ceiling | 4. Furnished and lit: the kitchen (Sunday, 12:13) |
|---|---|
| ![Mansion interior with brick walls and tiled floor](img/44-mansion-materials.jpg) | ![Mansion kitchen with generated furniture and lights](img/45-mansion-kitchen.jpg) |

The first screenshot is an untitled map with 57 box actors. The last one is the same house on Sunday morning with 267 actors. The furniture came from the same picture-to-model pipeline as the lobby props, and the working switches, bulbs and fuse box from the feature log were wired in afterwards.

## Feature log

Each entry in the diary has four parts: what we asked, how the AI helped, the result, and before/after captures. This is the short version.

### Electrical props

The fuse box, switches, socket and bulb came out of Meshy from images alone, with no text prompt: 14 models and 42 PBR textures, which the agent imported into 8 Blueprints and two maps.

| Untextured in Meshy | Textured in Meshy | In Unreal, with every state |
|---|---|---|
| ![Grey fuse box in Meshy](img/04-meshy-before.jpg) | ![Textured fuse box in Meshy](img/05-meshy-after.jpg) | ![Fuse box and switch states in Unreal](img/06-meshy-in-unreal.jpg) |

Two things the AI got wrong and a person caught by looking: the "healthy" and "burnt" fuse interiors were swapped relative to their file names, and colours bled between regions of the texture atlas until mipmaps were disabled.

### Electricity

We asked for smaller panels, a closer camera for wiring, water that becomes live from an exposed socket, fuses you can pick up, and a Phillips screwdriver. On swapping a fuse with the power on, the instruction was, literally: *"yes, let them swap it, but the fuse gets damaged too, hahaha"*. So the swap completes, the player takes damage and the spare burns.

After playing it, the feedback was blunt: connecting a wire was too hard and the automatic zoom was annoying. The next pass replaced both. Hold `E` to open the cover with a progress bar, press `E` again to inspect, then drag the wire tips by hand. Join red to green with the power on and you get a short circuit: the wire is consumed, the fuse burns, and health drops from 100 to 90.

| Live water from an exposed socket | Red to green, power on |
|---|---|
| ![Electrified puddle](img/07-electrified-water.jpg) | ![Short circuit](img/08-short-circuit.jpg) |

### Pipes and pressure

Pipes can be removed with a wrench, replaced, rotated and sealed. A designer can place them freely and link them to a valve by reference instead of by physical contact. Open a valve slowly and it is safe. Leave it fully open for six seconds and two or three pipes burst, which relieves the pressure and costs the crew money. Slam it open with `F` and exactly one pipe bursts, guaranteed.

| Pressure building, red warning ring | Two pipes burst, $70 of damage |
|---|---|
| ![Pressure building](img/11-pressure-building.jpg) | ![Pipes burst](img/12-pipes-burst.jpg) |

### Lights that really switch off

Load centre, then switch, then bulb, then real lights in the level. The switch cuts its own bulbs; the load centre cuts the whole circuit. A missing bulb, a burnt fuse or incomplete wiring each keeps the room dark.

| Main breaker off | Main breaker on |
|---|---|
| ![Breaker off](img/15-breaker-off.jpg) | ![Breaker on](img/16-breaker-on.jpg) |

Inside Mansion we first built one working set for the team to duplicate, then three teaching cases in the attic: one complete, one with the wires missing, one with wires and bulb missing.

| Attic, three circuits without power | Repaired and powered |
|---|---|
| ![Attic off](img/23-attic-off.jpg) | ![Attic on](img/24-attic-on.jpg) |

### Graffiti and the flashlight

Three of our images became cleanable graffiti. The spray runs out halfway, so you refill it from a finite bottle and carry on.

| Three graffiti on the wall | Cleaned: 90 points, $180 |
|---|---|
| ![Graffiti](img/21-graffiti-before.jpg) | ![Cleaned wall](img/22-graffiti-cleaned.jpg) |

The flashlight took three tries. At 250 lumens the centre of the beam was pure white. At 35 it still washed out the wall. The final value is 5 lumens, with the emitter moved 18 cm forward so the flashlight stops casting its own shadow.

| First attempt, 250 lumens | Final, 5 lumens |
|---|---|
| ![Flashlight at 250 lumens](img/19-flashlight-250lm.jpg) | ![Flashlight at 5 lumens](img/20-flashlight-5lm.jpg) |

### The ghost

A replicated, server-authoritative director that appears, hides, watches, and attacks when provoked or when someone lingers. The flashlight repels it some of the time, never reliably. It sabotages the job at random: it opens a valve, cuts the lights for 3 to 6 seconds, or flicks the main breaker. Each attempt has a 55% chance and a cooldown of 35 to 55 seconds, so it does not happen in every encounter.

At 18:27 on Sunday the ghost moved from its test map into the Mansion mission. It now reacts to the crew: it grows more aggressive as repairs are completed and as players talk, its damage rises from 25 to 45 and its speed from 250 to 330, and transmitting on the radio provokes it more than talking nearby.

| The ghost in its test map | A ghost blackout |
|---|---|
| ![Ghost](img/25-ghost.jpg) | ![Ghost blackout](img/26-ghost-blackout.jpg) |

### Lobby, menus and UI

- **Vote board.** A blueprint-style board with hand-written type and chalk outlines. Browsing does not vote; only the host can start the job.
- **Main menu.** First built with two 3D characters rendered into a transparent layer at 30 captures per second. We replaced it with an illustrated 2D layer that sways and breathes through a UI material, which removed every skeletal mesh and scene capture from the menu.
- **Illustrated workshop UI.** Cream paper, dark blue ink, yellow actions. The supply dropdown became shop cards with picture, price and stock.
- **Tutorial and loading.** A practice map reached from HOW TO PLAY?, with four stations, and a loading transition that closes like an iris around the logo with a spinning gear.
- **Voice.** Hold `T` for proximity voice up to 20 m, and hold `Q` for a crew-wide radio with a walkie-talkie filter generated in code. Keys can be rebound, with conflict checks.
- **Settings.** Display mode, resolution, three quality levels, master and effects volume. A display change reverts by itself after 15 seconds unless you confirm it.

| Vote board before | Vote board after |
|---|---|
| ![Vote board before](img/02-vote-board-before.jpg) | ![Vote board after](img/03-vote-board-after.jpg) |

| Shop card before | Shop after |
|---|---|
| ![Shop before](img/29-shop-before.jpg) | ![Shop after](img/30-shop-after.jpg) |

| Main menu | Voice, radio and key settings |
|---|---|
| ![Main menu](img/01-main-menu.jpg) | ![Settings](img/31-voice-settings.jpg) |

| Loading iris | Tutorial, four stations done |
|---|---|
| ![Loading iris](img/27-loading-iris.jpg) | ![Tutorial complete](img/28-tutorial-complete.jpg) |

## What broke, and what we did about it

This is the part of the diary we are proudest of, because none of it was tidied away.

1. **We lost the Mansion map in a merge.** A map is a binary file and Git cannot merge it. In the early hours of Saturday two people's work collided on it. The history shows four recovery merges between 05:03 and 05:33 to bring back the Mansion map together with the pipe and electricity work. On Sunday the Mansion circuit configuration went missing and had to be restored; the cause was never confirmed. The written rule is that each map has a single owner.
2. **The audit passed and the screwdriver was still wrong.** The agent recoloured the Phillips screwdriver blue and its audit confirmed the size and the material reference. After restarting the editor the old orange one was back. After a forced save the audit passed again, and the screwdriver on screen was grey: the material had failed to compile. The fix was a different shader node. Our note that night: *checking references and measurements does not replace looking at the result.*

   | Passed the audit, visibly wrong | Final |
   |---|---|
   | ![Grey screwdriver](img/09-screwdriver-shader-fail.jpg) | ![Blue and orange screwdrivers](img/10-screwdriver-fixed.jpg) |

3. **`E` let you into the wiring view and never let you out.** Before fixing it, the agent wrote a check that reproduced the bug. That report says `passed: true` and `exited_with_E: false`: it passed at proving the bug existed. Only then was it fixed.

   | Stuck in the wiring view | `E` exits, cover stays open |
   |---|---|
   | ![Stuck](img/13-stuck-in-wiring-view.jpg) | ![Exit fixed](img/14-exit-fixed.jpg) |

4. **Our own capture tool lied.** The lighting captures came out wrong. The diagnosis pointed at the tool that took them: holding a key for 0.12 seconds repeated the press on every frame and could toggle a switch twice. We fixed the tool, not the game.
5. **The guest could not see the host's tool.** A tool that was equipped and hidden never reached the second player. It now keeps its carrier's owner and network relevance while held.
6. **Elbow pipes were out of reach.** Reach was measured to a pivot placed high up (Z ≈ 353 cm). It is now measured to the pipe's oriented bounding box, and walls still block.

   | Cannot interact from the floor | "Hold E: Remove pipe" |
   |---|---|
   | ![Elbow unreachable](img/17-elbow-unreachable.jpg) | ![Elbow reachable](img/18-elbow-reachable.jpg) |

7. **The vote board ignored the mouse.** A decorative plane's simplified collision intercepted the trace about 8 cm over the widget, so no click arrived. Earlier tests used precise traces and never saw it.
8. **A test compared the wrong pipes.** It matched pipes by name across two worlds, and the names did not correspond. It now uses network IDs and checks position. The failed run is kept and is not counted as a pass.
9. **A demo failed twice because of its own setup.** First it tried to pick up a wrench the character already held. Then it stood the character inside a staircase. The third run approached from the free side and passed. Runs one and two are still in the repository marked `passed: false`.
10. **The ghost could never be provoked.** It needed two seconds of eye contact, but it retreated after 1.4.
11. **Screwdrivers bought in the shop arrived as fuses.** Fixed in the delivery code.
12. **The tutorial character slid without animating,** while the same character walked correctly in the mission. The tutorial's game mode had not assigned it.
13. **Spray cans almost a metre tall.** 95 cm in the tutorial; now 25 cm.
14. **Smaller ones.** A compile error from a pointer type in the UE 5.8 tests, a D3D12 crash while taking a "before" capture, a quality dropdown that did not apply "Low" while it was open, and a voice test that failed three times because its authorisation never reached the server; all three failed runs are kept.

## What we did not verify

Most checks ran in the editor, often with two players in the same editor session. Each diary entry ends with what it does not prove. At the time of most entries that was a cross-machine online session and a packaged build. Several demos sent input through Unreal's input system with the camera and position prepared, not physical key presses, and wires in the tutorial tests were connected through the server call, not by dragging with the mouse. Where a real key press was used, the entry says so.

## Sourced assets and licenses

3D models were generated from images with Meshy and fal. Images and some textures were generated with ChatGPT. Code was implemented and reviewed with ChatGPT and Codex.

| Audio | Author or source | License |
|---|---|---|
| Spray Bottle | OwlStorm, freesound.org/people/OwlStorm/sounds/212178 | CC0 1.0 |
| Hose Sounds | JazzyBay, freesound.org/people/JazzyBay/sounds/435052 | CC0 1.0 |
| Footsteps, mountain boots, rock, jump | Nox_Sound, freesound.org/people/Nox_Sound/sounds/558477 | CC0 1.0 |
| Screwing plastic screech (854) | Mixkit | Mixkit Sound Effects Free License |
| Wrench metal noises (853) | Mixkit | Mixkit Sound Effects Free License |
| Garage pneumatic screwer (817) | Mixkit | Mixkit Sound Effects Free License |
| Footsteps in a muddy puddle (536) | Mixkit | Mixkit Sound Effects Free License |
| Static electric crackles (1455) | Mixkit | Mixkit Sound Effects Free License |
| Water flowing (1303) | Mixkit | Mixkit Sound Effects Free License |

Fonts: Patrick Hand and Lilita One, both under the SIL Open Font License.

Apart from the audio and fonts listed here, everything in the game was generated during the jam with the AI tools above.

## Made before the jam

Nothing. The repository starts 13 minutes after the jam opened.

## If we keep building

- Run every feature again across machines and in the packaged build.
- Verify wire dragging with a real mouse in the automated tests.
- Save the crew's money and cargo to disk; today they last one session.
- Build the second job behind the "coming soon" card.
