---
title: Sandbox::Write(Day002) GLM, Actor, Component
author: Артём Воробьёв aka svirex
date: 2020-01-30 00:01:00 +0400
categories: [Coding, Sandbox]
---

## Класс `Actor`

Для работы с векторами, матрицами можно было бы написать свою библиотеку, для интереса. Но пока буду  использовать GLM. Качаю в папку вне проекта и добавляю строчки в `CMakeLists.txt`:

```cmake
include_directories(~/hdd/external_libs/glm/)
```

После этого определяю класс `Actor`:

```cpp
#ifndef SANDBOX_ACTOR_H
#define SANDBOX_ACTOR_H

#include <vector>

#include <glm/gtc/quaternion.hpp>
#include <glm/vec3.hpp>

class Game;
class Component;

class Actor {
public:
  enum State {
    EActive,
    EPaused,
    EDead
  };

  explicit Actor(Game *game);

  virtual ~Actor();

  void setPosition(glm::vec3 &position);

  void setRotation(glm::quat &rotation);

  void setScale(float scale);

  void addComponent(Component *component);

  void removeComponent(Component *component);

  void update(float deltaTime);

  void setState(State state);

  State getState() const;

protected:
  virtual void tick(float deltaTime);


private:
  glm::vec3 mPosition;

  glm::quat mRotation;

  float mScale;

  Game *mGame;

  std::vector<Component *> mComponents;

  State mState;

  void updateComponents(float deltaTime);
};

#endif // SANDBOX_ACTOR_H
```

Все акторы должны добавляться в вектор `mActors` в классе `Game`. Причем, так как класс `Game` вызывает обновление акторов из этого вектора, нам нельзя добавлять акторов на этапе обновления. Поэтому необходимо определить булево значение, которое определяет, обновляются ли акторы. Если да, то добавлять акторов в другой вектор `mPendingActors`, из которого будем переносить акторов в `mActors` после того, как завершим обновлять всех акторов.

После мы должны удалить всех акторов, у которые состояние стало `EDead`.

## Класс `Component`

Класс `Actor` содержит вектор `Component`, который определяется:

```cpp
#ifndef SANDBOX_COMPONENT_H
#define SANDBOX_COMPONENT_H

class Actor;

class Component {
public:
  explicit Component(Actor *owner, int updateOrder = 100);

  virtual ~Component();

  int getUpdateOrder() const;

  virtual void update(float deltaTime);

protected:
  Actor *mOwner;

private:
  int mUpdateOrder;
};

#endif // SANDBOX_COMPONENT_H
```

Компоненты обновляются в порядке, определенном переменной `updateOrder` в конструкторе.

Класс `Game` пробегает по своим акторам, вызывая метод `Actor::update` для каждого. Актор в свою очередь вызывает обновление всех своих компонентов и затем метод `Actor::tick`, который определяет специфичное для наследников обновление.

## Заключение

Написаны классы `Actor`, `Component`. Сделано обновление акторов.

[Код дня на Github](https://github.com/Svirex/sandbox/tree/SandboxDay002)
