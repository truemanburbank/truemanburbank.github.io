---
title: Interface와 Json 역직렬화, 직렬화
date: 2024-11-11
categories: [문법, C#]
tags: [Json, Interface]
---
Creature의 특성을 구현하기 위해 Factory Pattern과 Strategey Pattern을 사용했다. (이전 글 참고)

이후, UI_BaseCard에서 전투에 필요한 공통 기능들을 구현하였고, Creature Data를 이용하여 전투를 진행하는 식으로 설계했다. 따라서 Creature Data를 다음과 같이 구현하였다. 

```csharp
    public class CreatureData
    {
        public Action OnDataRefreshAction;
        public Action OnDefenceAction;
        public Action OnHitAction;
        public Action OnDeadAction;

        **public CreatureClass.ITrait Trait { get; set; }**
        public string Class { get; set; }
        public string Name { get; set; }
        public float MaxHP { get; set; }
        public float CurHP { get; set; }
        public float Attack { get; set; }
        public float Defence { get; set; }
        public float AttackSpeed { get; set; }
        public float DefenceSpeed { get; set; }
        public float Critical { get; set; }
        public float CriticalAttack { get; set; }
        public bool IsDefence { get; set; }
        public bool IsCritical { get; set; }
        public string IdleAnimStr { get; set; }
        public string AttackAnimStr { get; set; }
        public string BattleParticleAttack { get; set; }
        public string BattleParticleHit { get; set; }
    }

```

눈여겨 볼 것은 Action과 Trait이고, 나머지는 Creature의 공통 속성이다.

# 문제점 1

CreatureData를 상속받는 PlayerData를 Json으로 저장하려고 할 때 `type is an interface or abstract class and cannot be instantiated`라는 에러가 떴다. JSON 역직렬화는 기본적으로 **구체적인 클래스**의 인스턴스를 생성하려고 시도하기 때문에, 인터페이스나 추상 클래스를 직접 역직렬화할 수 없기 때문이었다.

```csharp
// 간단한 예시

void Save()
{
	 string jsonStr = JsonConvert.SerializeObject(PlayerData, Formatting.Indented, new JsonSerializerSettings
   {
       TypeNameHandling = TypeNameHandling.Objects,
       TypeNameAssemblyFormatHandling = TypeNameAssemblyFormatHandling.Simple,
   });
   
   File.WriteAllText(_path, jsonStr);
}

void Load()
{
	 string fileStr = File.ReadAllText(_path);
   CurPlayerData data = JsonConvert.DeserializeObject<CurPlayerData>(fileStr, new JsonSerializerSettings
   {
       TypeNameHandling = TypeNameHandling.Objects
   });
}
```

- `TypeNameHandling = TypeNameHandling.Objects` 옵션을 설정하여 JSON 데이터에 클래스 정보를 포함시킨다. JSON.NET은 객체를 JSON으로 변환할 때, 인터페이스나 추상 클래스를 직렬화할 수 없지만. 대신, **타입 정보**를 추가로 저장하도록 설정하면 이를 해결할 수 있었다.
- 직렬화 시 `TypeNameAssemblyFormatHandling = TypeNameAssemblyFormatHandling.Simple` 옵션을 설정했다. 불필요한 어셈블리 정보(예: `Version`, `Culture`, `PublicKeyToken`)를 제거함으로써 JSON 데이터를 더욱 간결하게 했다.

```json
// 기존 $type 정보
 "$type": "Namespace.PlayerData, AssemblyName, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"
 
// 간결해진 $type 정보
"$type": "Namespace.PlayerData, AssemblyName",
```

# 문제점 2

Data 안에 Action을 넣어서 발생하는 문제였는데, 발생한 이유는 다음과 같다.

- 델리게이트는 **메서드에 대한 참조**로, 런타임에만 의미를 가진다.
- JSON은 데이터를 저장하는 형식이므로 메서드 참조를 표현하거나 저장할 방법이 없다.

따라서, Action을 직렬화하지 않도록 [JsonIgnore] 속성을 추가함으로써 성공적으로 마무리했다.

# 최종 데이터 구조

```csharp
    public class CreatureData
    {
        [JsonIgnore]
        public Action OnDataRefreshAction;
        [JsonIgnore]
        public Action OnDefenceAction;
        [JsonIgnore]
        public Action OnHitAction;
        [JsonIgnore]
        public Action OnDeadAction;

        public CreatureClass.ITrait Trait { get; set; }
        public string Class { get; set; }
        public string Name { get; set; }
        public float MaxHP { get; set; }
        public float CurHP { get; set; }
        public float Attack { get; set; }
        public float Defence { get; set; }
        public float AttackSpeed { get; set; }
        public float DefenceSpeed { get; set; }
        public float Critical { get; set; }
        public float CriticalAttack { get; set; }
        public bool IsDefence { get; set; }
        public bool IsCritical { get; set; }
        public string IdleAnimStr { get; set; }
        public string AttackAnimStr { get; set; }
        public string BattleParticleAttack { get; set; }
        public string BattleParticleHit { get; set; }
    }

```