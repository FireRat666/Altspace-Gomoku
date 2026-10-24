# Gomoku (Five in a Row) for Banter

This is a fully multiplayer and synchronized version of the classic game Gomoku (also known as Five in a Row) designed to be easily embedded into any Banter world.

The game state is synced across all players in the space using Banter's shared state properties, so everyone sees the same game board and can take turns playing.

## Features

- **Fully Multiplayer:** Play with anyone in your Banter space.
- **Synchronized State:** The game board is always in sync for all players.
- **Customizable:** Easily change the board's size, position, rotation, and scale.
- **Simple to Embed:** Just add one line of code to your `index.html`.
- **Reset Button:** A built-in reset button allows players to start a new game at any time.

## How to Use

To add the Gomoku game to your Banter space, simply add the following script tag to your `index.html` file.

```html
<!-- Add this to the <body> of your index.html -->
<script src="https://banter-gomoku.firer.at/Gomoku.js"></script>
```

This will load the game with its default settings.

## SideQuest / Altspace Creator SDK (Snippet)

In a SideQuest/Altspace Creator SDK world, add an empty GameObject at the origin, click **Add Component > BS > Snippet** and type `gomoku`. Or paste it into `Assets/WebRoot/index.html` between the snippet section comments:

```html
<!-- snippet section -->
<bs-snippet name="gomoku" title="Gomoku"
  description="Synced multiplayer upright Gomoku (five in a row) board with a reset button."
  script="https://gomoku.firer.at/snippet.js"
  room=""
  position="0 1.5 2"
  rotation="0 0 0"
  scale="1"
  board-size="15"
  lighting="unlit"
  add-lights="true"
  hide-ui="false"
  hide-board="false"
  use-custom-models="false"
  use-ao-baking="false"
  reset-position="0 -1.2 0">
  <bs-gizmo type="position" attribute="position"/>
  <bs-gizmo type="plane" attribute="position" size="1.5 1.5"/>
</bs-snippet>
<!-- end snippet section -->
```

- Settings are the same as the script attributes above. `position` and `rotation` are Unity world values, and `scale` can be a single number.
- Each placement is its own game. Its sync id is `room`, or the placement's own id when `room` is empty. Give placements the same `room` to share one game.
- An empty setting uses the game's default.
- The Scene-view gizmo shows the board at its default size; it does not follow `scale`.
- Unity reads the snippet section as XML: quote every attribute, close every element, and write `&` as `&amp;`.

## Customization

You can customize the game using **HTML script attributes** (recommended) or legacy **URL query parameters** (for backwards compatibility). This allows you to change the appearance and placement of the game to fit your world perfectly.

**HTML Script Attributes (Recommended):**
```html
<script src="https://banter-gomoku.firer.at/Gomoku.js" boardPosition="0 1.5 -3" boardScale="0.8" boardSize="19"></script>
```

**Legacy URL Query Parameters:**
```html
<script src="https://banter-gomoku.firer.at/Gomoku.js?boardPosition=0+1.5+-3&boardScale=0.8&boardSize=19"></script>
```

### Available Attributes / Parameters

| Parameter / Attribute | Type        | Default Value         | Description                                                                                                   |
|-------------------|-------------|-----------------------|---------------------------------------------------------------------------------------------------------------|
| `instance`        | `string`    | (current URL)         | A unique ID for the game instance. Use this if you have multiple Gomoku boards in the same world.             |
| `boardSize` / `board-size`       | `integer`   | `15`                  | The size of the board (e.g., `15` for 15x15, `19` for 19x19).                                                 |
| `hideUI` / `hide-ui`          | `boolean`   | `false`               | Set to `true` to hide the reset button.                                                                       |
| `hideBoard` / `hide-board`       | `boolean`   | `false`               | Hides the board geometry.                                                                                     |
| `useCustomModels` / `use-custom-models` | `boolean`   | `false`               | Use GLB models for pieces.                                                                                    |
| `lighting`        | `string`    | `unlit`               | Shader type: 'lit' or 'unlit'.                                                                                |
| `addLights` / `add-lights`       | `boolean`   | `true`                | Add directional light for lit mode.                                                                           |
| `useAOBaking` / `use-ao-baking`     | `boolean`   | `false`               | Bake Ambient Occlusion on board geometry.                                                                     |
| `boardPosition` / `position` / `board-position`   | `Vector3`   | `0 1.5 0`             | The position of the game board in your world (X Y Z, space-separated).                                        |
| `boardRotation` / `rotation` / `board-rotation`   | `Vector3`   | `0 0 0`               | The rotation of the game board in Euler angles (X Y Z, space-separated).                                      |
| `boardScale` / `scale` / `board-scale`      | `Vector3`   | `1 1 1`               | The scale of the game board (X Y Z, space-separated).                                                         |
| `resetPosition` / `reset-position`   | `Vector3`   | `0 -1.2 0`            | The position of the reset button relative to the board.                                                       |
| `resetRotation` / `reset-rotation`   | `Vector3`   | `0 0 0`               | The rotation of the reset button.                                                                             |
| `resetScale` / `reset-scale`      | `Vector3`   | `1 1 1`               | The scale of the reset button.                                                                                |

**Note on Vector3:** When using legacy URL query parameters, coordinates must be URL-encoded or formatted with plus signs (e.g., `boardPosition=0+1.5+-3`). When using HTML script attributes, you can write them normally with spaces (e.g., `boardPosition="0 1.5 -3"`).

## How It Works

The game logic is contained within `Gomoku.js`. It uses the Banter SDK (`BS`) to create and manage all the visual elements of the game, including the board, the grid lines, and the game pieces (stones).

Player moves are handled by simulating the game state locally and then sending the new state to all players using `BS.BanterScene.GetInstance().SetPublicSpaceProps()`. This function stores the game's state (the board layout, current player, and win condition) in a shared JSON object.

When any player's client receives an update to this shared state via the `space-state-changed` event, it re-renders the board to reflect the latest move, ensuring everyone stays in sync.
