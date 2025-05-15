# Chapter 1 Questions (Instructor)

[Back to Chapter 1 Summary](./README.md)

## Instructor's Questions for Flyweight Design Patterns

### Problem

In a conceptual 2D tile-based game, you need to display numerous instances of environmental objects like "OakTree", "PineTree", and "GraniteRock". Each object type has specific **intrinsic properties** such as:
- **Sprite representation** (e.g., "oaktree.png", "pinetree.png", "graniterock.png")
- **Default dimensions** (e.g., width: 32px, height: 32px)

However, each individual tree or rock instance has **extrinsic properties** such as:
- Unique `(x, y)` coordinates on the game map
- Variations in rotation, scale, or health status

### Task

#### 1. Define `IGameAssetFlyweight` Interface
- Declare a `render` method:
  ```typescript
  render(x: number, y: number, rotation: number, scale: number, currentHealth?: number): void;
  ```

#### 2. Implement `ConcreteGameAsset` Class
- This class implements `IGameAssetFlyweight`.
- **Intrinsic State**:
  - `assetName: string` (e.g., "OakTree")
  - `spriteUrl: string`
  - `defaultWidth: number`
  - `defaultHeight: number`
- **Render Method**:
  - Simulate rendering by logging:
    ```
    Rendering ${this.assetName} (Sprite: ${this.spriteUrl}) at (${x}, ${y}), Rot: ${rotation}deg, Scale: ${scale}, Health: ${currentHealth || 'N/A'}
    ```

#### 3. Create `AssetFactory` Class
- Manages and shares `ConcreteGameAsset` instances.
- **Methods**:
  - `getAsset(assetName: string, spriteUrl: string, defaultWidth: number, defaultHeight: number): IGameAssetFlyweight`
    - Use `assetName` as the cache key if `spriteUrl` and dimensions are tied to the name, or a combination if they can vary.
  - `getFlyweightCount(): number`

#### 4. Create `GameLevel` Client Class
- Maintains a list of assets to be rendered.
- **Asset List Entry**:
  ```typescript
  { 
    assetFlyweight: IGameAssetFlyweight, 
    x: number, 
    y: number, 
    rotation: number, 
    scale: number, 
    health: number 
  }
  ```
- **Methods**:
  - `addAssetToLevel(assetName: string, spriteUrl: string, defaultWidth: number, defaultHeight: number, x: number, y: number, rotation: number, scale: number, health: number): void`
    - Get the `IGameAssetFlyweight` from the `AssetFactory`.
    - Store the flyweight with its extrinsic state `(x, y, rotation, scale, health)` in the asset list.
  - `renderLevel()`
    - Iterate over all assets in the level.
    - Call the `render` method on each asset's flyweight, passing the extrinsic state.

#### 5. Demonstration
- Instantiate `GameLevel`.
- Add multiple instances of "OakTree" and "GraniteRock" at different positions, rotations, and scales using `addAssetToLevel`. For example:
  - Add 5 "OakTree" instances.
  - Add 3 "GraniteRock" instances.
- Call `renderLevel()`.
- Log:
  - Total number of asset instances on the map.
  - Number of unique flyweight objects created by the `AssetFactory`.
    - Example: "8 total assets, but only 2 flyweights: one for 'OakTree' and one for 'GraniteRock'."

---

This exercise demonstrates the Flyweight pattern in a game development context. It highlights how textures and sprites are shared, introduces a more complex set of extrinsic states, and generalizes the pattern beyond simple character rendering.