---
title: Sandbox::Write(Day001) Костяк песочницы, основной игровой цикл
author: Артём Воробьёв aka svirex
date: 2020-01-27 00:01:00 +0400
categories: [Coding, Sandbox]
---

Итак, стартую написание песочницы! В посте будет немного информации и кода.

## Основной игровой цикл

Основной игровой цикл состоит из трёх шагов:

1. Получение данных от устройств ввода
2. Обновление игрового мира
3. Генерация вывода (в данном случае изображения)

`core/Game.h`:

```cpp
#ifndef SANDBOX_GAME_H
#define SANDBOX_GAME_H

class Renderer;

class Game {
public:

  Game();

  bool initialize();

  void runLoop();

  void shutdown();

private:

  void processInput();

  void updateGame();

  void generateOutput();

  bool mIsRunning;

  bool mUpdatingActor;

  Renderer *mRenderer;
};

#endif // SANDBOX_GAME_H

```

В качестве библиотеки для работы с окнами, вводом-выводом использую SDL2.

Создадим класс `Renderer`, который будет отвечать за всё отображение, а на данном этапе - за инициализацию окна.

`core/Renderer.h`:

```cpp
#ifndef SANDBOX_RENDERER_H
#define SANDBOX_RENDERER_H

#include <SDL2/SDL.h>

class Game;

class Renderer {
public:
    Renderer(Game *game);
    ~Renderer() = default;

    bool initialize(float screenWidth, float screenHeight);

    void shutdown();

private:
    SDL_Window *mWindow;

    Game *mGame;

    float mScreenWidth;

    float mScreenHeight;
};

#endif // SANDBOX_RENDERER_H
```

Теперь надо сделать закрытие окна при нажатии на "крестик":

```cpp
void Game::processInput() {
  SDL_Event event;
  while(SDL_PollEvent(&event)) {
    switch (event.type) {
      case SDL_QUIT:
        mIsRunning = false;
        break;
    }
  }
}
```

## Заключение

Код ожидаемо получился похож на код из книги [Game Programming in C++: Creating 3D Games: Creating 3D Games (Game Design)](https://www.amazon.com/Game-Programming-Creating-Games-Design/dp/0134597206/), так как именно этот код я взял за основу. Дальше код будет так же похожим, костяк в этой книжке дан очень хорошо.

Сейчас открывается окно, можно его закрыть "крестиком". [Github code](https://github.com/Svirex/sandbox/tree/SandboxDay001)
