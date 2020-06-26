# WebGL-Tutorial
## Multiple Images Texture Mapping
### 201620896 윤태섭
# 1. 프로젝트의 주제
webGL에서 정육면체의 한 면마다 다른 명화의 이미지를 넣어본다.

# 2. texture mapping
컴퓨터 그래픽에서 가상의 3차원 물체의 표면을 2차원의 그림이나 수식으로 적용하여 실제 물체처럼 느껴지도록 하는 기법이다.

UV-mapping : u와 v를 사용하여 2차원의 그림을 3차원의 모델로 만드는 프로세스이다.

# 3. 방법
6개의 이미지를 정육면체의 한 면마다 적용하기 위해서 6가지의 texture를 각각 reference할 복잡한 shader를 만들고 fragment shader에 vertex당 사용할 texture를 결정하는 방법이 있다.
하지만 이 방법은 굉장히 복잡한 방식이 될 것이다. 조금 더 간단한 방법이 있다.
6개의 이미지를 하나의 texture에 넣는 방식이다. 6개의 명화를 하나의 texture에 넣으면 이런 그림이 된다.

![painting](/uploads/dea127c2d8d4ad11b966785ffe5f2e01/painting.png)

이 이미지는 저장될 때 좌에서 우로, 위에서 아래로 저장되어 있기 때문에 실제 작업을 할 때는 

![painting_reflect](/uploads/047d625d79d1aac385145ec8652917b3/painting_reflect.png)

이런 모양을 하고 있다고 생각해야 한다. 
이 texture를 texture corrdinates로 보면 아래의 그림처럼 표현된다.

![texturecoord](/uploads/f8c46bd21a9846a2fff46dec0b624dcb/texturecoord.PNG)

따라서 각 u,v를 통해 그림을 넣기 위해서는 위의 사항들을 고려햐여 결정해야 한다.  
위의 사항들을 고려하여 정육면체의 각 면에 각각의 명화를 texture mapping한다.

![cube1](/uploads/b9bcb65e0926bef51d0e124eacad060f/cube1.PNG)  ![cube2](/uploads/8acde5c7da5d9035923e89584bac568c/cube2.PNG)

# 4. 코드

fragment shader에서 정육면체의 입혀질 color를 texture로 바꾸기 위해 gl_FragColor = 0.0 * color + 1.0 * texture2D(sampler2d, texCoord)로 한다. 

    var fragmentShaderSource = '\
            varying highp vec4 color; \
            varying mediump vec2 texCoord;\
            uniform sampler2D sampler2d; \
			void main(void) \
			{ \
                gl_FragColor = 0.0 * color + 1.0 * texture2D(sampler2d, texCoord); \
			}';
        
vertex shader에 texCoord = myUV 를 넣는다. 

    var vertexShaderSource = '\
			attribute highp vec4 myVertex; \
            attribute highp vec4 myColor; \
            attribute highp vec2 myUV; \
			uniform mediump mat4 mmat; \
			uniform mediump mat4 vmat; \
			uniform mediump mat4 pmat; \
            varying highp vec4 color;\
            varying mediump vec2 texCoord;\
			void main(void)  \
			{ \
                gl_Position = pmat*vmat*mmat * myVertex; \
                color = myColor; \
                texCoord = myUV; \
			}';