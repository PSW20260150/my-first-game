# week03 실습기록
##오늘의 목표1:오류가 난 코드를 ai를 통해 수정하고, 각 줄이 어떤 역할을 하는지, 설명을 부탁했다. 그리하여 완성된 코드는
import pygame
import sys

pygame.init()

screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("My First Pygame")

WHITE = (255, 255, 255)
BLUE = (0, 0, 255)

clock = pygame.time.Clock()
running = True

while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    screen.fill(WHITE)
    pygame.draw.circle(screen, BLUE, (400, 300), 50)

    pygame.display.flip()
    clock.tick(60)

pygame.quit()
sys.exit()


이다. 고쳐지기전 코드에는 3이라는 문법적으로 필요없는 숫자(오류를 범하는 숫자)와 각 줄의 들여쓰기 문제 때문에 실행이 되질 않았었는데, 챗지피티가 수정해준 덕에 정상 작동을 하였다.각 줄의 역할은 색상정의,게임루프,프레임제한같은 역할이었다.여기서 나는 궁금증이 생겨서 프레임제한을 60에서 10,15로 바꿔가며 관찰했지만, 원은 가만히 있었기 때문에 차이를 느끼지 못하였다. 그래서 나는 다음 목표로 넘어가기로 하였다


##오늘의 목표2:키보드를 통한 입력으로 원을 움직이게 하기


나는 프레임 차이를 느끼기 위하여 프레임을 표시하고, 키보드의 상하좌우 키를 통하여 움직이게 하였다, 교수님의 말씀대로  "움직인다"와 같은 애매한 구조가 아닌 5픽셀만큼 움직이게 해달라고 부탁했다. 그랬더니 코드가 잘 나왔다


import pygame
import sys

pygame.init()

# 화면 설정
screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("My First Pygame")

# 색상 정의
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
BLACK = (0, 0, 0)

# FPS 표시용
clock = pygame.time.Clock()
font = pygame.font.SysFont(None, 30)

# 원 초기 위치
x, y = 400, 300
radius = 50
speed = 5  # 이동 속도

running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 키 입력 확인
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        x -= speed
    if keys[pygame.K_RIGHT]:
        x += speed
    if keys[pygame.K_UP]:
        y -= speed
    if keys[pygame.K_DOWN]:
        y += speed

    # 화면 그리기
    screen.fill(WHITE)
    pygame.draw.circle(screen, BLUE, (x, y), radius)

    # FPS 표시
    fps = int(clock.get_fps())
    fps_text = font.render(f"FPS: {fps}", True, BLACK)
    screen.blit(fps_text, (10, 10))

    pygame.display.flip()
    clock.tick(60)  # 60 FPS 고정

pygame.quit()
sys.exit()


이런식의 세세한 코드가 나왔다. 기존과의 차이점이라면 각 키에 대응하는 코드, (K_LEFT)같은 문법과, 원의 위치, 움직이는 속도,fps_text를 통해 fps를 띄우고 (10,10)이라는 좌표값을 통한 상단에 띄우는것까지 생겼다.솔직히 처음 이 코드를 받아보고는 이해가 안되어 지피티에게 연신 질문하기 바빴었다. 그래도 지피티 덕분에 이해가 잘 되었다. 특히 내가 색상 관련해서 지워버리니까 화면이 안보이는 오류가 낫었는데, 지피티가 천천히 설명해 주어 색상을 바꿔 볼 수 잇었다. 그 욍도 질문을 더 했다


### AI대화기록


Q1: 각각 질문을 색상을 변경할 때는 단순히 이름만 바꿔서는 안되는거야?

AI 답변: 변수 이름은 Python 규칙을 지켜야 함 (_ 사용, 숫자로 시작 X 등)

적용 결과: 성공

Q2: 화면의 크기를 키우고 싶은데, (800,600)을 바꾸는 맞아?

AI 답변: 맞음, pygame.display.set_mode((가로, 세로))에서 크기 변경 가능

적용 결과: 화면은 커짐

Q3: Pygame에서 창이 없어서 종료가 불편한데 어떻게 해야 하나?

AI 답변 1: pygame.display.set_mode((800, 600))를 사용하면 창 모드로 실행됨

이해 안됨 / 재질문: 창 모드로 바꿨는데 여전히 종료 버튼이 안보임 → 어떻게 종료하지?

시행착오: pygame.display.set_mode((800, 600), pygame.RESIZABLE) 옵션을 써서 창 모드로 바꾸려 했으나, 코드 위치와 초기화 순서 때문에 제대로 적용되지 않음

최종 해결: pygame.display.set_mode((800, 600), pygame.RESIZABLE)를 pygame 초기화 직후, 화면 설정할 때 바로 적용 → 창 모드 활성화, 종료 버튼 정상 작동


####배운점


내가 코딩은 거의 처음이라서 수업에 잘 따라가지 못할까봐 연신 걱정했는데, 다행히 lms에 올려진 친절한 교수님의 강의영상과 ai의 도움으로 해결할 수 잇었다, 이번 주차에서 나는 여러가지를 알앗지만, 그 중 제일 기억에 남는것은 색상의 변경이다. 비록 색상 변경이 누군가에게는 밥먹듯이 쉬울 정도의 코드 수준일 수 있고, 하는 법을 배운 지금이라면 이렇게 간단하게 변경 가능한 코드도 딱히 없응 것 같다고 생각한다. 그러나 처음 색상을 바꿀때 단순히 이름만 바꾸었다가 실행하니 화면이 안보여서 많이 당황했었고, 이게 궁금해 지피티에게 물어보니 색상을 변경할때는 변수이름은 상관이 없고, 그 변수에 대응되는 숫자가 색깔에 따라 다르게 대응되야 한다는것을 알고 여러 색상으로 바꿔보앗고, 결과는 대성공적이었다. 비록 지피티라는 고성능ai의 도움을 받긴했지만, 내가 코드를 처음으로 수정해 보앗다는 사실 덕에 가장 기억에 남고, 배운것중에 가장 재미있고 유익했다고 생각한다

