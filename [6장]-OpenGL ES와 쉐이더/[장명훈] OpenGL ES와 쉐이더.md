# Vertex Shader

![image](https://github.com/user-attachments/assets/39c5c0ed-ed4f-43ea-8b00-5684560a2b3f)

버택스 정보에는 다음과 같이 구성되어 있다.

- Position
- normal
- tex coord(8장)
![image](https://github.com/user-attachments/assets/440692ad-2022-4f39-978c-6334a3e510fb)

우리는 저 정점들이 순차적으로 처리된다고 생각해도 된다. 실제로 GPU에선 저 정점들이 병렬적으로 처리되고 있다.

우리는 OpenGL 라이브러리를 가져다 쓰지만 두 가지는 우리가 코딩해야 한다. 바로 버택스 셰이더와 프래그먼트 셰이더이다. 이를 줄여서 OpenGL ES Shading Language라 해서 GLSL이라 한다. 

버택스 셰이더는 두 가지 입력을 받는다.

- Attributes : 버택스마다 갖고 있는 정보 ex) Position, normal, tex coord
- Uniforms : 모든 버택스가 공통으로 가지고 있는 정보 ex) world Transform, view transform, projection transform

어떤 물체를 회전하면 그 물체를 이루는 버택스들은 하나의 기준 축을 기준으로 회전변환이 일어나는데 여기에 쓰이는 월드 변환은 공통이다. 

이렇게 변환을 거친 정점들은 클립공간에 들어간다. 이는 버택스 셰이더의 의무이다.

```cpp
// 클립 스페이스 정점의 위치를 저장하고 있다.
gl_position();
```

```cpp
#version 400 core

uniform mat4 worldMat, viewMat, projMat;

// Attributes
// layout : 각각의 attribute가 어떤 위치에 저장되어 있는지 나타냄
layout(location = 0) int vec3 position;
layout(location = 1) int vec3 normal;
layout(location = 2) int vec2 texCoord;

// 출력
out vec3 v_normal;
out vec2 v_texCoord;

void main()
{	
	// vec4(position, 1.0) : 오브젝트 스페이스 좌표
	// 1.0 : 4*4 행렬을 맞추기 위해 동차 좌표계
	// gl_Position = 클립 스페이스
	gl_Position = projMat * viewMat * worldMat * vec4(position,1.0);
	v_normal = normalize(transpose(inverse(mat3(worldMat))) * normal);
	v_texCoord = texCoord;
}
```

- 위치 변환

```cpp
// vec4(position, 1.0) : 오브젝트 스페이스 좌표
	// 1.0 : 4*4 행렬을 맞추기 위해 동차 좌표계
	// gl_Position = 클립 스페이스
	gl_Position = projMat * viewMat * worldMat * vec4(position,1.0);
```

예전에 배운 걸 복습해보자.

노말은 일반적인 월드변환을 적용하면 안된다. 대신 월드 변환의 [L|t]의 L부분만 고려. 그리고 역행렬을 곱한다. 

```cpp
v_normal = normalize(transpose(inverse(mat3(worldMat))) * normal);
```

## 코드 설명

openGL 특징

- GL 함수 : 접두사 gl_
- GL 자료형 : 접두사 GL
1. 셰이더 오브젝트 생성
    1. 2개의 셰이더 오브젝트를 만든다.
    
    ```cpp
    GLuint VertexShaderID = glCreateShader(GL_VERTEX_SHADER);
    glShaderSource(VertexShaderID, 1, &VertexSourcePointer, NULL);
    glCompileShader(VertexShaderID);
    ```
    
2. program Object에 통합
    1. 프로그램에 Attach한다.
    
    ```cpp
    //Link the program
    GLuint ProgramID = glCreateProgram();
    glAttachShader(ProgramID, VertexShaderID);
    glAttachShader(ProgramID, FragmentShaderID);
    glLinkProgram(ProgramID);
    glUseProgram(programID);
    ```
    

### Attributes

- vertices : 버택스 배열을 가리키는 포인터
- indices : 인덱스 배열을 가리키는 포인터

![image](https://github.com/user-attachments/assets/e33af811-d158-4b3c-8301-698ccd1b986d)

이제 CPU에서 데이터는 만들었고 GPU에 이를 전달해야 한다. GPU에는 버퍼 오브젝트가 있고 위 두 가지를 받는다.

```cpp
glBindBuffer(GL_ARRAY_BUFFER, vertexBufferID[0]);
```


- 버택스 배열

버퍼 오브젝트는 이를 받아서 generate해야 한다.
```cpp
GLuint vertexBufferID[2];           // VBO, 위치, 색 이렇게 2개
glGenBuffers(2, vertexBufferID);        // VBO 생성
// vertex array로 쓸 버퍼
glBindBuffer(GL_ARRAY_BUFFER, vertexBufferID[0]);
// GPU에게 위치 데이터 전달 - 버퍼에게 데이터를 주겠다 해서 glBufferData
glBufferData(GL_ARRAY_BUFFER, g_points.size() * sizeof(float), g_points.data(), GL_STATIC_DRAW);    
```

- 인덱스 배열

버택스 배열과 동일
![image](https://github.com/user-attachments/assets/5174b89f-9742-44cf-8b58-e3068ad61c02)


어떻게 읽어들여야 하는지 알려줘야 한다. 여기서 어느 크기만큼 읽어들일지 stride를 정해줘야 한다.
![image](https://github.com/user-attachments/assets/9da85c44-bb73-40d9-b2a0-46028185763f)
```cpp
glVertexAttribPointer(posLoc, 3, GL_FLOAT, GL_FALSE, 0, (void*)(0));    // 데이터 할당
glEnableVertexAttribArray(posLoc);
```
### Uniform

오브젝트마다 월드 행렬은 다 다를 거다. 따로 설정해줘야 한다. 

### DrawCalls

매 프레임마다 그리라고 알리는 함수 호출

- 버택스만 있는 경우

```cpp
// count : 버택스 배열 크기
glDrawArrays (GLenum mode, GLint first, GLsizei count);
```

주어진 예시에선 삼각형 48개 * 꼭지점 개수 3개 = 144개 버택스

- 인덱스 어레이

```cpp
glDrawElements (GLenum mode, GLsizei count, GLenum type, const void *indices);
```

인덱스 어레이도 144개

```cpp
glDrawElements(GL_TRIANGLES, 144, GL_UNSIGNED_SHORT, 0)
```


