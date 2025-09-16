# 重构原则

1. 单一职责原则 (SRP): 每个类或方法只负责一件事。
2. 开闭原则 (OCP): 对扩展开放，对修改关闭。
3. 保持行为一致: 重构后功能不变。
4. 避免重复 (DRY): 消除重复代码。
5. 封装变化: 将易变部分封装。
6. 小步快走: 逐步、小规模重构。
7. 提高可读性: 让代码更易理解。
8. 减少耦合: 降低模块之间的依赖。
9. 高内聚: 让每个模块内部逻辑紧密相关。
10. 持续重构: 定期优化代码。
11. 避免过度设计: 避免不必要的复杂性。
12. 测试驱动 (TDD): 确保重构前后通过测试。



重构的原则是为了确保代码在重构过程中保持稳定性，同时提高其可读性、可维护性和扩展性。以下是重构的一些关键原则：

### 1. **单一职责原则 (SRP)**
每个类或方法应该只有一个明确的职责。如果一个类承担了多个职责，应该考虑拆分。这样可以使代码更易于理解和维护，避免类因职责过多而变得庞大复杂。

### 2. **开闭原则 (OCP)**
代码应该对扩展开放，对修改关闭。即在添加新功能时，尽量通过扩展现有的代码（例如继承或实现接口）来实现，而不是修改已有的代码。这可以减少对现有代码的影响，降低引入新问题的风险。

### 3. **保持行为一致**
在重构过程中，必须保证代码的外部行为保持不变。可以通过编写单元测试或使用现有测试来确保重构后的代码与之前的功能一致。

### 4. **避免重复 (DRY - Don't Repeat Yourself)**
消除代码中的重复部分是重构的重要目标之一。将重复的代码提取到一个通用的函数、类或模块中，可以减少维护成本和错误的可能性。

### 5. **封装变化**
对于容易变化的部分，应该封装起来。通过将容易变化的逻辑独立到类或方法中，当需求改变时只需要修改少量代码，从而减少对整个系统的影响。

### 6. **小步快走**
重构应该是渐进式的，逐步进行而非一次性大规模改动。每次改动应该足够小，以便容易测试和验证。如果某一步出现问题，能够很容易回滚到之前的状态。

### 7. **提高可读性**
代码是给人看的，因此重构时应该尽量提高代码的可读性。重构后代码应该更容易理解，包括简化复杂的逻辑、使用有意义的命名等。

### 8. **减少耦合**
尽量降低代码之间的耦合度，使各个模块独立运作。低耦合可以使代码的维护和修改更加容易，因为修改一个模块不容易影响到其他模块。

### 9. **高内聚**
每个类或模块中的功能应该紧密相关，高内聚的代码更容易理解、测试和维护。如果类中包含了与核心功能无关的逻辑，可以通过重构将它们移除或分解到新的类中。

### 10. **持续重构**
重构不是一次性的任务，而是一个持续的过程。随着项目的发展，代码需要不断优化以适应新的需求和技术环境，保持代码的清晰、简洁和可维护性。

### 11. **避免过度设计**
重构时要避免过度设计，不要为了未来可能的扩展而引入过于复杂的结构。重构应该针对当前实际的问题，而非假设的未来需求。

### 12. **测试驱动 (TDD)**
测试驱动开发（TDD）可以帮助确保在重构过程中不引入新问题。在重构前后，应该确保所有的单元测试通过，这样可以保证代码功能的一致性。

### 示例场景：如何应用这些原则

假设你有一个游戏中的 `Player` 类，它既包含玩家的移动逻辑，又包含攻击逻辑，还处理生命值和经验系统。这个类显然违反了**单一职责原则**，因为它承担了多个职责。为了重构，你可以：

1. 根据**单一职责原则**，将移动、攻击、和状态管理（生命值、经验）分离到不同的类中。
2. 使用**开闭原则**，通过继承或接口，扩展攻击方式，而不是修改已有的代码。
3. 避免重复代码，确保移动逻辑、攻击逻辑可以复用，以**避免重复**。

通过遵循这些重构原则，你的代码会变得更清晰、更具模块化，并且更容易扩展和维护。

以下是添加了注释的重构示例，所有示例都与Unity编程相关：

### 1. **单一职责原则 (SRP)**

**例子**：在 `Enemy` 类中将不同职责拆分为独立类，避免类承担过多的责任。

**重构前**：
```csharp
public class Enemy : MonoBehaviour
{
    public float health;
    public float moveSpeed;
    public Animator animator;

    void Update()
    {
        Move(); // 处理移动逻辑
        Attack(); // 处理攻击逻辑
        UpdateAnimations(); // 处理动画更新
    }

    void Move() { /* 移动逻辑 */ }
    void Attack() { /* 攻击逻辑 */ }
    void UpdateAnimations() { /* 动画逻辑 */ }
}
```

**重构后**：将不同的职责分配到不同类中。
```csharp
public class Enemy : MonoBehaviour
{
    private EnemyMovement movement; // 专门处理敌人的移动
    private EnemyAttack attack; // 专门处理敌人的攻击
    private EnemyAnimation animation; // 专门处理敌人的动画

    void Update()
    {
        movement.Move(); // 调用移动类的方法
        attack.PerformAttack(); // 调用攻击类的方法
        animation.UpdateAnimations(); // 调用动画类的方法
    }
}
```

### 2. **开闭原则 (OCP)**

**例子**：在 `Enemy` 类中扩展不同类型的敌人攻击方式时不修改现有代码，而是通过继承扩展。

**重构前**：
```csharp
public class Enemy
{
    public void Attack()
    {
        if (type == "Melee")
            PerformMeleeAttack(); // 处理近战攻击
        else if (type == "Ranged")
            PerformRangedAttack(); // 处理远程攻击
    }
}
```

**重构后**：使用继承扩展不同攻击方式，遵循开闭原则。
```csharp
public abstract class Enemy
{
    public abstract void Attack(); // 攻击方法抽象
}

public class MeleeEnemy : Enemy
{
    public override void Attack() { PerformMeleeAttack(); } // 实现近战攻击
}

public class RangedEnemy : Enemy
{
    public override void Attack() { PerformRangedAttack(); } // 实现远程攻击
}
```

### 3. **保持行为一致**

**例子**：在重构 `Player` 的移动逻辑时，确保重构前后行为一致。

**重构前**：
```csharp
public class Player : MonoBehaviour
{
    public void Move(Vector3 direction)
    {
        transform.position += direction * Time.deltaTime * speed; // 处理玩家移动
    }
}
```

**重构后**：将移动逻辑封装到 `PlayerMovement` 类中，并验证行为一致性。
```csharp
public class PlayerMovement
{
    public void Move(Transform transform, Vector3 direction, float speed)
    {
        transform.position += direction * Time.deltaTime * speed; // 处理移动
    }
}
```
> **测试**：确保 `PlayerMovement.Move()` 方法的最终结果与之前一致。

### 4. **避免重复 (DRY)**

**例子**：将重复的伤害计算逻辑提取到一个接口中，以减少代码重复。

**重构前**：
```csharp
public class Player
{
    public void TakeDamage(int amount) { health -= amount; } // 玩家受到伤害
}

public class Enemy
{
    public void TakeDamage(int amount) { health -= amount; } // 敌人受到伤害
}
```

**重构后**：创建一个 `Damageable` 接口并在玩家和敌人中复用。
```csharp
public interface IDamageable
{
    void TakeDamage(int amount); // 定义通用的伤害方法
}

public class Player : IDamageable
{
    public void TakeDamage(int amount) { health -= amount; } // 玩家实现伤害逻辑
}

public class Enemy : IDamageable
{
    public void TakeDamage(int amount) { health -= amount; } // 敌人实现伤害逻辑
}
```

### 5. **封装变化**

**例子**：使用策略模式封装敌人AI行为，简化复杂的AI逻辑。

**重构前**：
```csharp
public class Enemy
{
    public void UpdateAI()
    {
        if (type == "Patrol")
            Patrol(); // 巡逻AI
        else if (type == "Chase")
            Chase(); // 追逐AI
    }
}
```

**重构后**：将不同的AI行为封装到独立的类中。
```csharp
public interface IEnemyAI
{
    void UpdateAI(); // 定义AI更新行为
}

public class PatrolAI : IEnemyAI
{
    public void UpdateAI() { Patrol(); } // 实现巡逻AI
}

public class ChaseAI : IEnemyAI
{
    public void UpdateAI() { Chase(); } // 实现追逐AI
}

public class Enemy
{
    private IEnemyAI enemyAI;

    public void SetAI(IEnemyAI ai) { enemyAI = ai; } // 设置AI行为
    public void UpdateAI() { enemyAI.UpdateAI(); } // 更新AI行为
}
```

### 6. **小步快走**

**例子**：逐步重构 `Player` 移动代码，从小改动开始，然后逐步优化。

**重构前**：
```csharp
public class Player : MonoBehaviour
{
    public void Move()
    {
        // 复杂移动逻辑
        transform.position += direction * Time.deltaTime * speed; // 更新位置
    }
}
```

**重构过程**：
1. **第一步**：将 `speed` 提取到单独的方法。
2. **第二步**：逐步将移动逻辑拆分到 `PlayerMovement` 类中。

```csharp
public class PlayerMovement
{
    public float GetSpeed() { return speed; } // 第一步：优化速度计算逻辑
    public void Move(Transform transform, Vector3 direction)
    {
        transform.position += direction * Time.deltaTime * GetSpeed(); // 第二步：拆分移动逻辑
    }
}
```

### 7. **提高可读性**

**例子**：通过重命名提高代码的可读性，避免不明含义的变量名和方法名。

**重构前**：
```csharp
public class GameManager
{
    public void P(int a, int b) { /* 游戏初始化 */ }
}
```

**重构后**：重命名变量和方法，使其具备清晰的描述性。
```csharp
public class GameManager
{
    public void InitializeGame(int playerCount, int level) { /* 游戏初始化 */ }
}
```

### 8. **减少耦合**

**例子**：通过接口减少 `GameManager` 对 `Player` 和 `Enemy` 的直接依赖，降低耦合度。

**重构前**：
```csharp
public class GameManager
{
    public Player player;
    public Enemy enemy;

    void Update()
    {
        player.Move(); // 控制玩家移动
        enemy.Attack(); // 控制敌人攻击
    }
}
```

**重构后**：使用接口进行解耦。
```csharp
public interface ICharacter
{
    void PerformAction(); // 定义角色的通用行为
}

public class Player : ICharacter
{
    public void PerformAction() { Move(); } // 玩家实现移动
}

public class Enemy : ICharacter
{
    public void PerformAction() { Attack(); } // 敌人实现攻击
}

public class GameManager
{
    private List<ICharacter> characters = new List<ICharacter>();

    void Update()
    {
        foreach (var character in characters)
        {
            character.PerformAction(); // 统一控制角色行为
        }
    }
}
```

### 9. **高内聚**

**例子**：将与玩家移动相关的所有逻辑集中在 `PlayerMovement` 类中，确保内聚性。

**重构前**：
```csharp
public class Player : MonoBehaviour
{
    public void Move() { /* 移动逻辑 */ }
    public void HandleJump() { /* 跳跃逻辑 */ }
}
```

**重构后**：将所有移动相关逻辑集中到 `PlayerMovement` 中。
```csharp
public class PlayerMovement
{
    public void Move() { /* 处理移动逻辑 */ }
    public void Jump() { /* 处理跳跃逻辑 */ }
}
```
### 10. **持续重构**

**例子**：游戏中不断增加新的攻击方式，使用持续重构的方式逐步优化代码结构。

**重构过程**：

**重构前**：最初的攻击逻辑可能只是简单地包含几种硬编码的攻击方式。
```csharp
public class Player
{
    public void Attack(string attackType)
    {
        if (attackType == "Melee")
        {
            PerformMeleeAttack(); // 处理近战攻击
        }
        else if (attackType == "Ranged")
        {
            PerformRangedAttack(); // 处理远程攻击
        }
    }
}
```

**重构后**：使用策略模式和工厂模式重构，便于扩展新的攻击方式。
```csharp
public abstract class AttackStrategy
{
    public abstract void Attack(); // 定义通用攻击接口
}

public class MeleeAttack : AttackStrategy
{
    public override void Attack() { PerformMeleeAttack(); } // 实现近战攻击
}

public class RangedAttack : AttackStrategy
{
    public override void Attack() { PerformRangedAttack(); } // 实现远程攻击
}

// 工厂类，用于创建不同的攻击策略
public class AttackFactory
{
    public static AttackStrategy GetAttackStrategy(string attackType)
    {
        if (attackType == "Melee")
            return new MeleeAttack(); // 返回近战攻击策略
        else if (attackType == "Ranged")
            return new RangedAttack(); // 返回远程攻击策略
        else
            throw new ArgumentException("Invalid attack type");
    }
}

public class Player
{
    public void PerformAttack(string attackType)
    {
        AttackStrategy attackStrategy = AttackFactory.GetAttackStrategy(attackType); // 使用工厂获取攻击策略
        attackStrategy.Attack(); // 执行攻击
    }
}
```
**持续重构**：随着新的攻击方式的引入，例如魔法攻击，只需添加一个新的策略类，代码的其他部分无需修改。
```csharp
public class MagicAttack : AttackStrategy
{
    public override void Attack() { PerformMagicAttack(); } // 实现魔法攻击
}

// 在攻击工厂中添加新的攻击策略
public class AttackFactory
{
    public static AttackStrategy GetAttackStrategy(string attackType)
    {
        if (attackType == "Melee")
            return new MeleeAttack();
        else if (attackType == "Ranged")
            return new RangedAttack();
        else if (attackType == "Magic")
            return new MagicAttack(); // 新增魔法攻击
        else
            throw new ArgumentException("Invalid attack type");
    }
}
```

通过持续重构，我们不仅保持了代码的清晰性，还提高了扩展性，允许未来的修改和新增功能变得更加简便和安全。

### 11. **避免过度设计**

**例子**：在设计一个简单的玩家射击游戏时，最初玩家只有一种武器。你不需要一开始就设计复杂的武器系统。

**避免过度设计**：
如果将所有武器设计为独立的类，并用工厂模式生成各种武器，可能会导致代码复杂度增加，而目前的需求只需要一种基本武器。在这种情况下，过早的优化会导致设计过度。

### 12. **测试驱动 (TDD)**

**例子**：在修改 `Player` 的生命值系统时，通过编写单元测试来验证生命值减少、增加和死亡的逻辑，确保重构后的代码依然正常工作。

**重构前**：
直接修改代码，没有测试保障。

**重构后**：编写测试确保功能一致。
```csharp
[Test]
public void PlayerHealth_ReducesOnDamage()
{
    Player player = new Player();
    player.TakeDamage(10);
    Assert.AreEqual(90, player.health);
}
```
