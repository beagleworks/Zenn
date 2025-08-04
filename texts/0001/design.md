# 設計書

## 概要

ブラウザテトリスは、HTML5 CanvasとJavaScriptを使用して実装される、クラシックなテトリスゲームの拡張版です。標準的な7種類のテトロミノに加えて、モノミノ、ドミノ、トリオミノを含む11種類のブロックタイプを提供し、プレビューブロックのスキップ機能を備えています。

## アーキテクチャ

### 全体構成
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   HTML/CSS      │    │   Game Engine   │    │   Rendering     │
│   - Canvas      │◄──►│   - Game Loop   │◄──►│   - Canvas API  │
│   - UI Elements │    │   - State Mgmt  │    │   - Animation   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         ▲                        ▲                        ▲
         │                        │                        │
         ▼                        ▼                        ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Input Handler │    │   Block System  │    │   Audio System  │
│   - Keyboard    │    │   - Tetrominos  │    │   - Sound FX    │
│   - Touch       │    │   - Collision   │    │   - Background  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### レイヤー構造
1. **プレゼンテーション層**: HTML/CSS/Canvas描画
2. **ゲームロジック層**: ゲームルール、状態管理
3. **データ層**: ブロック定義、ゲームフィールド

## コンポーネントとインターフェース

### 1. GameEngine クラス
```javascript
class GameEngine {
    constructor(canvas)
    start()
    pause()
    resume()
    gameLoop()
    update(deltaTime)
    render()
    handleGameOver()
}
```

### 2. BlockSystem クラス
```javascript
class BlockSystem {
    generateRandomBlock()
    getCurrentBlock()
    getNextBlock()
    skipCurrentBlock()
    rotateBlock()
    moveBlock(direction)
    checkCollision(block, x, y)
}
```

### 3. GameField クラス
```javascript
class GameField {
    constructor(width, height)
    placeBlock(block, x, y)
    checkLines()
    clearLines(lines)
    isGameOver()
    getFieldState()
}
```

### 4. ScoreManager クラス
```javascript
class ScoreManager {
    addScore(lines)
    updateLevel()
    getScore()
    getLevel()
    getLinesCleared()
    getSkipStacks()
    useSkipStack()
    addSkipStack()
}
```

### 5. InputHandler クラス
```javascript
class InputHandler {
    bindEvents()
    handleKeyDown(event)
    handleKeyUp(event)
    handleTouchEvents()
}
```

### 6. Renderer クラス
```javascript
class Renderer {
    constructor(canvas)
    drawGameField()
    drawCurrentBlock()
    drawNextBlock()
    drawUI()
    drawGameOver()
    drawPauseScreen()
}
```

## データモデル

### ブロック定義
```javascript
const BLOCKS = {
    I: { shape: [[1,1,1,1]], color: '#00FFFF' },
    O: { shape: [[1,1],[1,1]], color: '#FFFF00' },
    T: { shape: [[0,1,0],[1,1,1]], color: '#800080' },
    S: { shape: [[0,1,1],[1,1,0]], color: '#00FF00' },
    Z: { shape: [[1,1,0],[0,1,1]], color: '#FF0000' },
    J: { shape: [[1,0,0],[1,1,1]], color: '#0000FF' },
    L: { shape: [[0,0,1],[1,1,1]], color: '#FFA500' },
    MONO: { shape: [[1]], color: '#FFFFFF' },
    DOMINO: { shape: [[1,1]], color: '#FFB6C1' },
    TRIO_L: { shape: [[1,0],[1,1]], color: '#DDA0DD' },
    TRIO_I: { shape: [[1,1,1]], color: '#98FB98' }
};
```

### ゲーム状態
```javascript
const gameState = {
    field: Array(20).fill().map(() => Array(10).fill(0)),
    currentBlock: null,
    nextBlock: null,
    blockX: 0,
    blockY: 0,
    score: 0,
    level: 1,
    linesCleared: 0,
    skipStacks: 4,
    gameStatus: 'playing', // 'playing', 'paused', 'gameOver'
    dropTimer: 0,
    dropInterval: 1000
};
```

## エラーハンドリング

### 1. 入力エラー
- 無効なキー入力の無視
- 境界外移動の防止
- 衝突時の操作キャンセル

### 2. ゲーム状態エラー
- 不正な状態遷移の防止
- ゲームフィールド境界チェック
- ブロック生成失敗時の処理

### 3. レンダリングエラー
- Canvas描画エラーのキャッチ
- フレームレート低下時の対応
- リサイズ時の再描画

### エラー処理パターン
```javascript
try {
    // ゲーム操作
    this.moveBlock(direction);
} catch (error) {
    console.warn('Block movement failed:', error);
    // 操作をキャンセルし、前の状態を維持
    this.revertBlockPosition();
}
```

## テスト戦略

### 1. 単体テスト
- **BlockSystem**: ブロック生成、回転、衝突検出
- **GameField**: ライン消去、ゲームオーバー判定
- **ScoreManager**: スコア計算、レベル更新
- **InputHandler**: キー入力処理

### 2. 統合テスト
- ゲームループ全体の動作
- ブロック配置からライン消去までの流れ
- スキップ機能とスタック管理の連携

### 3. ユーザビリティテスト
- キー操作の応答性
- 視覚的フィードバックの適切性
- モバイルデバイスでの操作性

### 4. パフォーマンステスト
- 60FPSでの安定動作
- メモリリークの検証
- 長時間プレイでの安定性

### テスト実装例
```javascript
// 単体テストの例
describe('BlockSystem', () => {
    test('should generate valid block types', () => {
        const blockSystem = new BlockSystem();
        const block = blockSystem.generateRandomBlock();
        expect(Object.keys(BLOCKS)).toContain(block.type);
    });
    
    test('should detect collision correctly', () => {
        const blockSystem = new BlockSystem();
        const field = new GameField(10, 20);
        // 境界での衝突テスト
        expect(blockSystem.checkCollision(block, -1, 0)).toBe(true);
    });
});
```

## 実装の詳細設計

### ゲームループ設計
```javascript
class GameLoop {
    constructor() {
        this.lastTime = 0;
        this.accumulator = 0;
        this.fixedTimeStep = 16.67; // 60 FPS
    }
    
    loop(currentTime) {
        const deltaTime = currentTime - this.lastTime;
        this.lastTime = currentTime;
        this.accumulator += deltaTime;
        
        while (this.accumulator >= this.fixedTimeStep) {
            this.update(this.fixedTimeStep);
            this.accumulator -= this.fixedTimeStep;
        }
        
        this.render();
        requestAnimationFrame(this.loop.bind(this));
    }
}
```

### 衝突検出アルゴリズム
```javascript
checkCollision(block, x, y, rotation = 0) {
    const shape = this.getRotatedShape(block, rotation);
    
    for (let row = 0; row < shape.length; row++) {
        for (let col = 0; col < shape[row].length; col++) {
            if (shape[row][col]) {
                const newX = x + col;
                const newY = y + row;
                
                // 境界チェック
                if (newX < 0 || newX >= this.field.width || 
                    newY >= this.field.height) {
                    return true;
                }
                
                // ブロック衝突チェック
                if (newY >= 0 && this.field.grid[newY][newX]) {
                    return true;
                }
            }
        }
    }
    return false;
}
```

### スキップ機能の実装
```javascript
class SkipManager {
    constructor() {
        this.maxStacks = 4;
        this.currentStacks = 4;
        this.linesForRecovery = 2;
        this.linesSinceLastRecovery = 0;
    }
    
    canSkip() {
        return this.currentStacks > 0;
    }
    
    useSkip() {
        if (this.canSkip()) {
            this.currentStacks--;
            return true;
        }
        return false;
    }
    
    onLinesCleared(lines) {
        this.linesSinceLastRecovery += lines;
        
        while (this.linesSinceLastRecovery >= this.linesForRecovery && 
               this.currentStacks < this.maxStacks) {
            this.currentStacks++;
            this.linesSinceLastRecovery -= this.linesForRecovery;
        }
    }
}
```