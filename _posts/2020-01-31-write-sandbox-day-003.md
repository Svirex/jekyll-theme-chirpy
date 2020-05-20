---
title: Sandbox::Write(Day003) OpenGL, Shader
author: Артём Воробьёв aka svirex
date: 2020-01-31 00:01:00 +0400
categories: [Coding, Sandbox]
---

## Инициализация контекста OpenGL

Для начала необходимо создать контекст OpenGL и инициализировать окно для работы с ним. Метод `Renderer::initialize` примет вид:

```cpp
bool Renderer::initialize(float screenWidth, float screenHeight) {
  mScreenWidth = screenWidth;
  mScreenHeight = screenHeight;

  SDL_GL_SetAttribute(SDL_GL_CONTEXT_PROFILE_MASK, SDL_GL_CONTEXT_PROFILE_CORE);
  SDL_GL_SetAttribute(SDL_GL_CONTEXT_MAJOR_VERSION, 3);
  SDL_GL_SetAttribute(SDL_GL_CONTEXT_MINOR_VERSION, 3);
  SDL_GL_SetAttribute(SDL_GL_RED_SIZE, 8);
  SDL_GL_SetAttribute(SDL_GL_GREEN_SIZE, 8);
  SDL_GL_SetAttribute(SDL_GL_BLUE_SIZE, 8);
  SDL_GL_SetAttribute(SDL_GL_ALPHA_SIZE, 8);
  SDL_GL_SetAttribute(SDL_GL_DEPTH_SIZE, 24);
  SDL_GL_SetAttribute(SDL_GL_DOUBLEBUFFER, 1);
  SDL_GL_SetAttribute(SDL_GL_ACCELERATED_VISUAL, 1);

  mWindow = SDL_CreateWindow("Sandbox", 100, 100, static_cast<int>(screenWidth),
                             static_cast<int>(screenHeight), SDL_WINDOW_OPENGL);
  if (!mWindow) {
    SDL_Log("Failed to create window: %s", SDL_GetError());
    return false;
  }

  mContext = SDL_GL_CreateContext(mWindow);

  // Initialize GLEW
  glewExperimental = GL_TRUE;
  if (glewInit() != GLEW_OK) {
    SDL_Log("Failed to initialize GLEW.");
    return false;
  }

  glGetError();

  return true;
}
```

## Класс `Shader`

Теперь опишем класс `Shader`, кторый будет загружать, компилировать и активировать шейдерную программу на GPU:

```cpp
#ifndef SANDBOX_SHADER_H
#define SANDBOX_SHADER_H

#include <string>

#include <GL/glew.h>

class Shader {
public:
  Shader();

  ~Shader() = default;

  bool load(const std::string &vertexShaderFile, const std::string &fragmentShaderFile);

  void unload();

  void setActive();

private:
  GLuint mVertexShader;

  GLuint mFragmentShader;

  GLuint mShaderProgram;

  bool compileShader(const std::string &filePath, GLenum shaderType, GLuint &outShader);

  bool isCompiled(GLuint shader);

  bool isValidProgram(GLuint program);
};

#endif // SANDBOX_SHADER_H
```

## Код шейдеров

И напишем для теста два простых шейдера.

Вершинный:

```glsl
#version 330 core

uniform mat4 uWorldTransformation;
uniform mat4 uViewProjection;

layout(location = 0) in vec3 position;

out vec4 vertexColor;

void main() {
    vec4 pos = vec4(position, 1.0);

    gl_Position = uViewProjection * uWorldTransformation * pos;

    vertexColor = vec4(0.3, 0.3, 0.3, 1.0);
}
```

Фрагментный:

```glsl
#version 330 core

in vec4 vertexColor;

out vec4 color;

void main() {
    color = vertexColor;
}
```

Данные шейдеры компиляться без ошибок.

## Заключение

[Код дня на гитхабе](https://github.com/Svirex/sandbox/tree/SandboxDay003)
