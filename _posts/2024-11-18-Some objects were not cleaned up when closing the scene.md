---
title: Some objects were not cleaned up when closing the scene. (Did you spawn new GameObjects from OnDestroy?)
date: 2024-11-18
categories: [Etc, TIL]
tags: [troubleshooting]
---
# 해결

`@Managers`와 `@Cursor`가 게임 종료 시 완전히 삭제되지 않는 버그가 일어났다. 원인은 싱글톤 패턴을 사용할 때, `@Managers`와 `@Cursor` 객체가 null이면 새로운 객체를 생성하는 코드였다. `OnDestory()`시 `Managers.Game.~` 와 같이 매니저 객체를 다시 부르게 되면 새로운 객체를 생성하여 이러한 오류가 뜨는 것이다. 

<aside>
💡

`OnDestory`에서 `Manager` 객체를 부르는 코드를 수정했다.

</aside>

# 문제가 되었던 코드

### Managers.cs

```csharp
    public static void Init()
    {
        if (s_instance == null)
        {
            GameObject go = GameObject.Find("@Managers");
            if (go == null)
            {
                go = new GameObject { name = "@Managers" };
                go.AddComponent<Managers>();
            }

            GameObject cursor = GameObject.Find("@Cursor");
            if (cursor == null)
            {
                cursor = new GameObject { name = "@Cursor" };
                cursor.AddComponent<SpriteRenderer>();
                cursor.AddComponent<Animator>();
                cursor.AddComponent<CursorManager>();
            }

            DontDestroyOnLoad(go);
            DontDestroyOnLoad(cursor);
            s_instance = go.GetComponent<Managers>();
        }
    }
```

### UI_Fade.cs

```csharp
    private void OnDestroy()
    {
        Managers.Game.OnFade = false;
    }
```