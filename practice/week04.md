# week04 실습기록

## 실습1

##Pygame으로 800x600 창을 만들어줘.
회색 사각형 오브젝트 2개를 그려줘.
하나는 방향키로 이동 가능, 하나는 화면 중앙 고정.
두 오브젝트의 AABB (빨간 사각형)를 함께 표시해줘.

나온코드

import pygame
import sys

pygame.init()

# 화면 설정
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("AABB Demo")

clock = pygame.time.Clock()

# 색상
GRAY = (150, 150, 150)
RED = (255, 0, 0)
BG = (30, 30, 30)

# 이동 가능한 사각형
player_size = (80, 60)
player_rect = pygame.Rect(100, 100, *player_size)
speed = 5

# 중앙 고정 사각형
center_size = (120, 80)
center_rect = pygame.Rect(
    WIDTH // 2 - center_size[0] // 2,
    HEIGHT // 2 - center_size[1] // 2,
    *center_size
)

running = True
while running:
    clock.tick(60)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 키 입력 처리
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_rect.x -= speed
    if keys[pygame.K_RIGHT]:
        player_rect.x += speed
    if keys[pygame.K_UP]:
        player_rect.y -= speed
    if keys[pygame.K_DOWN]:
        player_rect.y += speed

    # 화면 지우기
    screen.fill(BG)

    # 회색 사각형 그리기
    pygame.draw.rect(screen, GRAY, player_rect)
    pygame.draw.rect(screen, GRAY, center_rect)

    # AABB (빨간 테두리)
    pygame.draw.rect(screen, RED, player_rect, 2)
    pygame.draw.rect(screen, RED, center_rect, 2)

    pygame.display.flip()

pygame.quit()
sys.exit()

를 주었다, 이 코드를 사용하니 방향키로 이동 가능한 빨간 테두리를 가진 사각형 2개가 나왔다. 하나는 화면 가운데에 고정되있고 다른 하나는 방향키를 통해 조작 할 수 있었다
그러나 이 코드에서는 가운데에 고정된 사각형과 내가 조작 가능한 사각형이 서로 닿아도 겹쳐서 관통되었다.

 코드에서 AABB를 화면에 그리는 부분만 설명해달라 하자

pygame.draw.rect(screen, RED, player_rect, 2)
pygame.draw.rect(screen, RED, center_rect, 2)

이 코드 두줄만을 띄워주었다. 그러나 이 부분만 봣을때는 코드만 보이지 저 코드들이 무슨역할을 하는지는 모르기에 다음 질문을 또 하였다
 ygame.draw.rect 의 인자가 각각 뭘 의미하는지 포함해줘

surface, color, rect, width이 4개의 역할을 하나하나 설명해 주었다

surface은 사각형의 화면,color는 사각형의 색상, rect는 사각형의 위치와 크기를 담고 있는 객체, 마지막으로 width는 선의 두께이다 width의 값에 따라 사각형 내부가 채워지거나 테두리의 굵기를 조절 할 수 잇었다.

그런데 여기서 뮨득 AABB가 정확히 무엇인지 몰라 재차 질문하였다

그랬더니 gpt가 물체를 감싸는 축에 정렬된(기울어지지 않은) 사각형 박스입니다. 하고 알려주었다

그 외에 설명을 들으니 AABB는 그냥 단순한 기울어지지않는 사각형이라는 것을 알 수 있었다. 말 그대로 오브젝트를 감싸는 단순한 사각형이었다

import pygame
import sys
import math

pygame.init()

# 화면 설정
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("AABB + Circle Collision")

clock = pygame.time.Clock()

# 색상
GRAY = (150, 150, 150)
RED = (255, 0, 0)
BLUE = (0, 100, 255)
BG = (30, 30, 30)
YELLOW = (255, 255, 0)

# 이동 가능한 사각형
player_size = (80, 60)
player_rect = pygame.Rect(100, 100, *player_size)
speed = 5

# 중앙 고정 사각형
center_size = (120, 80)
center_rect = pygame.Rect(
    WIDTH // 2 - center_size[0] // 2,
    HEIGHT // 2 - center_size[1] // 2,
    *center_size
)

running = True
while running:
    clock.tick(60)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 이동 처리
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_rect.x -= speed
    if keys[pygame.K_RIGHT]:
        player_rect.x += speed
    if keys[pygame.K_UP]:
        player_rect.y -= speed
    if keys[pygame.K_DOWN]:
        player_rect.y += speed

    # 🔵 중심 좌표
    player_center = player_rect.center
    center_center = center_rect.center

    # 🔵 반지름 (너비의 절반)
    player_radius = player_rect.width // 2
    center_radius = center_rect.width // 2

    # 🔵 원형 충돌 판정 (거리 기반)
    dx = player_center[0] - center_center[0]
    dy = player_center[1] - center_center[1]
    distance = math.sqrt(dx * dx + dy * dy)

    circle_collision = distance < (player_radius + center_radius)

    # 배경색 변경
    if circle_collision:
        screen.fill(YELLOW)
    else:
        screen.fill(BG)

    # 회색 사각형
    pygame.draw.rect(screen, GRAY, player_rect)
    pygame.draw.rect(screen, GRAY, center_rect)

    # 🔴 AABB
    pygame.draw.rect(screen, RED, player_rect, 2)
    pygame.draw.rect(screen, RED, center_rect, 2)

    # 🔵 원형 Bounding Box
    pygame.draw.circle(screen, BLUE, player_center, player_radius, 2)
    pygame.draw.circle(screen, BLUE, center_center, center_radius, 2)

    pygame.display.flip()

pygame.quit()
sys.exit()

이 코드를 실행해 보니 사각형을 감싸는 원이 생겼고 원과 원이 맞다으면 화면이 노래지며 충돌을 감지하였다

원형 충돌 감지에서 math.sqrt 를 쓰는 이유가 뭐야?
에 대하여 gpt는 원형 충돌에서는 두 중심 사이의 거리를 구해야 하기 때문이라고 답하였다.
그러면 sqrt 없이도 구현이 가능할까? 궁금했기에 나는 물어보았다
그랬더니 오히려 더 쉽게 구현 가능하다 하였다. sqrt를 쓰는 상황은 정확한 거리가 필요할때이고, 평소에는 cpu를 많이 잡아먹기에 평소에는 sqrt를 사용하는게 훨씬 효율적이라고 하였다

오브젝트가 정사각형일 때 원형
Bounding Box의 모서리 부분에서
어떤 일이 일어나는가?

질문해 보니 가짜 충돌 판정이 일어난다 하였다. 이런 반응이 일어나는 이유는 사각형 기준으로는 서로 닿지않았지만 원 기준으로는 이미 반지름이 겹친 판정이기에 충돌이 발생했다고 인식하였다
AABB가 원형보다 먼저 충돌하는 경우가 궁금해서 물어보니, 측명상황에서는 사각형끼리 서로 먼저 닿지만, 원끼리 닿지 않았기에 충돌판정이 일어나지 않았다

## 실습2
ABBB말고 OBB를 알기 위해서
현재 코드에서 고정된 오브젝트가 천천히 회전하게 해줘.
Z 키를 누르면 회전 속도가 빨라지게 해줘.
회전하는 오브젝트의 OBB (초록 사각형)도 함께 표시해달라고 코드를 요구하였다
처음 받은 코드를 썻을때는 원과 몸체가 같이 돌아가였다. 그래서 몸체말고 OBB가 돌아가도록 수정을 부탁했고 

import pygame
import sys
import math

pygame.init()

# 화면 설정
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Bounding Box Only Rotation")

clock = pygame.time.Clock()

# 색상
GRAY = (150, 150, 150)
RED = (255, 0, 0)
BLUE = (0, 100, 255)
GREEN = (0, 255, 0)
BG = (30, 30, 30)
YELLOW = (255, 255, 0)

# 플레이어 (AABB)
player_size = 80
player_rect = pygame.Rect(100, 100, player_size, player_size)
speed = 5

# 중앙 고정 본체
center_size = 120
center_pos = (WIDTH // 2, HEIGHT // 2)

# 회전 변수 (Bounding Box)
angle = 0
rotation_speed = 1
fast_rotation = 5

# SAT 충돌 함수
def sat_collision(rect_corners, aabb_rect):
    ax, ay, aw, ah = aabb_rect
    aabb_corners = [
        (ax, ay),
        (ax + aw, ay),
        (ax + aw, ay + ah),
        (ax, ay + ah)
    ]

    def get_axes(corners):
        axes = []
        for i in range(4):
            p1 = corners[i]
            p2 = corners[(i + 1) % 4]
            edge = (p2[0] - p1[0], p2[1] - p1[1])
            normal = (-edge[1], edge[0])
            length = math.hypot(*normal)
            if length != 0:
                normal = (normal[0]/length, normal[1]/length)
            axes.append(normal)
        return axes

    axes = get_axes(rect_corners) + get_axes(aabb_corners)

    def project(corners, axis):
        dots = [corner[0]*axis[0] + corner[1]*axis[1] for corner in corners]
        return min(dots), max(dots)

    for axis in axes:
        min1, max1 = project(rect_corners, axis)
        min2, max2 = project(aabb_corners, axis)
        if max1 < min2 or max2 < min1:
            return False
    return True

running = True
while running:
    clock.tick(60)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 플레이어 이동
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_rect.x -= speed
    if keys[pygame.K_RIGHT]:
        player_rect.x += speed
    if keys[pygame.K_UP]:
        player_rect.y -= speed
    if keys[pygame.K_DOWN]:
        player_rect.y += speed

    # Bounding Box 회전 속도
    current_speed = fast_rotation if keys[pygame.K_z] else rotation_speed
    angle += current_speed
    angle %= 360

    # 🔵 원형 Bounding Box 회전
    player_center = player_rect.center
    center_center = center_pos
    player_radius = player_size // 2
    center_radius = center_size // 2
    dx = player_center[0] - center_center[0]
    dy = player_center[1] - center_center[1]
    distance_sq = dx*dx + dy*dy
    circle_collision = distance_sq < (player_radius + center_radius)**2

    # 🟢 OBB 꼭짓점 (본체는 회전하지 않음)
    cx, cy = center_center
    w, h = center_size, center_size
    rad = math.radians(-angle)
    cos_a = math.cos(rad)
    sin_a = math.sin(rad)
    obb_corners = []
    for dx_corner in [-w/2, w/2]:
        for dy_corner in [-h/2, h/2]:
            x = cx + dx_corner * cos_a - dy_corner * sin_a
            y = cy + dx_corner * sin_a + dy_corner * cos_a
            obb_corners.append((x, y))
    obb_points = [obb_corners[0], obb_corners[1], obb_corners[3], obb_corners[2]]

    # 🔴 OBB vs 플레이어 충돌
    obb_collision = sat_collision(obb_points, player_rect)

    # 배경색
    if obb_collision:
        screen.fill(RED)
    elif circle_collision:
        screen.fill(YELLOW)
    else:
        screen.fill(BG)

    # 중앙 정사각형 본체 (회전 안함)
    center_rect = pygame.Rect(0, 0, center_size, center_size)
    center_rect.center = center_center
    pygame.draw.rect(screen, GRAY, center_rect)

    # 플레이어
    pygame.draw.rect(screen, GRAY, player_rect)

    # 🔴 AABB
    pygame.draw.rect(screen, RED, player_rect, 2)
    pygame.draw.rect(screen, RED, center_rect, 2)

    # 🔵 원형 Bounding Box
    pygame.draw.circle(screen, BLUE, player_center, player_radius, 2)
    pygame.draw.circle(screen, BLUE, center_center, center_radius, 2)

    # 🟢 OBB (회전)
    pygame.draw.polygon(screen, GREEN, obb_points, 2)

    pygame.display.flip()

pygame.quit()
sys.exit()

이런 코드가 나왔다. 이 코드에서는 z키를 누르면 OBB가 평소보다 빠르게 회전하여 회전하는 OBB에 닿으면 충돌판정이 일어나 화면이 빨개졌다
그러나 이런 코드에서는 모서리와 모서리가 닿아도 충돌 반응이 일어나지 않았다 
그렇기에 OBB 충돌 감지를 SAT(분리축 정리)로 구현해달라고 요구했다

import pygame
import sys
import math

pygame.init()

# 화면 설정
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("AABB + SAT OBB + Circle")

clock = pygame.time.Clock()

# 색상
GRAY = (150, 150, 150)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 100, 255)
BG = (30, 30, 30)
YELLOW = (255, 255, 0)

# 플레이어 (AABB)
player_size = 80
player_rect = pygame.Rect(100, 100, player_size, player_size)
speed = 5

# 중앙 고정 본체 (정사각형)
center_size = 120
center_pos = (WIDTH // 2, HEIGHT // 2)

# 회전하는 Bounding Box (OBB)
obb_angle = 0
rotation_speed = 1
fast_rotation = 5

# SAT 충돌 함수
def sat_collision(corners1, corners2):
    def get_axes(corners):
        axes = []
        for i in range(4):
            p1 = corners[i]
            p2 = corners[(i + 1) % 4]
            edge = (p2[0]-p1[0], p2[1]-p1[1])
            normal = (-edge[1], edge[0])
            length = math.hypot(*normal)
            if length != 0:
                normal = (normal[0]/length, normal[1]/length)
            axes.append(normal)
        return axes

    axes = get_axes(corners1) + get_axes(corners2)

    def project(corners, axis):
        dots = [corner[0]*axis[0] + corner[1]*axis[1] for corner in corners]
        return min(dots), max(dots)

    for axis in axes:
        min1, max1 = project(corners1, axis)
        min2, max2 = project(corners2, axis)
        if max1 < min2 or max2 < min1:
            return False
    return True

# OBB 꼭짓점 계산
def get_obb_corners(center, size, angle_deg):
    cx, cy = center
    w, h = size
    rad = math.radians(angle_deg)
    cos_a = math.cos(rad)
    sin_a = math.sin(rad)
    corners = []
    for dx in [-w/2, w/2]:
        for dy in [-h/2, h/2]:
            x = cx + dx * cos_a - dy * sin_a
            y = cy + dx * sin_a + dy * cos_a
            corners.append((x, y))
    return [corners[0], corners[1], corners[3], corners[2]]

running = True
while running:
    clock.tick(60)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 플레이어 이동
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_rect.x -= speed
    if keys[pygame.K_RIGHT]:
        player_rect.x += speed
    if keys[pygame.K_UP]:
        player_rect.y -= speed
    if keys[pygame.K_DOWN]:
        player_rect.y += speed

    # OBB 회전 속도
    obb_angle += fast_rotation if keys[pygame.K_z] else rotation_speed
    obb_angle %= 360

    # 🔵 원형 Bounding Box
    player_center = player_rect.center
    center_center = center_pos
    player_radius = player_size // 2
    center_radius = center_size // 2
    dx = player_center[0] - center_center[0]
    dy = player_center[1] - center_center[1]
    distance_sq = dx*dx + dy*dy
    circle_collision = distance_sq < (player_radius + center_radius)**2

    # 🟢 회전 OBB
    obb_corners = get_obb_corners(center_center, (center_size, center_size), obb_angle)

    # 🔴 SAT 충돌
    player_corners = get_obb_corners(player_rect.center, (player_size, player_size), 0)
    obb_collision = sat_collision(obb_corners, player_corners)

    # 배경색
    if obb_collision:
        screen.fill(RED)
    elif circle_collision:
        screen.fill(YELLOW)
    else:
        screen.fill(BG)

    # 🔹 중앙 본체 정사각형 (회전 안함)
    center_rect = pygame.Rect(0, 0, center_size, center_size)
    center_rect.center = center_center
    pygame.draw.rect(screen, GRAY, center_rect)
    pygame.draw.rect(screen, RED, center_rect, 2)  # AABB 테두리

    # 🔹 플레이어 정사각형 (AABB)
    pygame.draw.rect(screen, GRAY, player_rect)
    pygame.draw.rect(screen, RED, player_rect, 2)  # AABB 테두리

    # 🔵 원형 Bounding Box
    pygame.draw.circle(screen, BLUE, player_center, player_radius, 2)
    pygame.draw.circle(screen, BLUE, center_center, center_radius, 2)

    # 🟢 회전 OBB
    pygame.draw.polygon(screen, GREEN, obb_corners, 2)

    pygame.display.flip()

pygame.quit()
sys.exit()

이런 코드가 나왔다. 이 코드는 모서리가 닿아도 SAT를 적용했기에 적용 전과 달리 충돌반응이 일어났다
그러나 이 코드에선 원이나 사걱형이 닿았는지 안닿았는지 모르기에 닿았는지를 알기 위해
현재 코드를 수정해서 세 가지 충돌 방식을 동시에 표시해줘.
- 원형: 파란 원, 충돌 시 상단에 “Circle: HIT” 표시
- AABB: 빨간 사각형, 충돌 시 “AABB: HIT” 표시
- OBB: 초록 사각형, 충돌 시 “OBB: HIT” 표시
세 방식의 충돌 결과를 화면 왼쪽 상단에 동시에 표시해달라고 요구 하였다

import pygame
import sys
import math

pygame.init()

# 화면 설정
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Multiple Collision Visualization")

clock = pygame.time.Clock()

# 색상
GRAY = (150, 150, 150)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 100, 255)
BG = (30, 30, 30)
WHITE = (255, 255, 255)
YELLOW = (255, 255, 0)

# 폰트
font = pygame.font.SysFont(None, 30)

# 플레이어 (AABB)
player_size = 80
player_rect = pygame.Rect(100, 100, player_size, player_size)
speed = 5

# 중앙 고정 본체
center_size = 120
center_pos = (WIDTH // 2, HEIGHT // 2)

# 회전 OBB
obb_angle = 0
rotation_speed = 1
fast_rotation = 5

# SAT 충돌 함수
def sat_collision(corners1, corners2):
    def get_axes(corners):
        axes = []
        for i in range(4):
            p1 = corners[i]
            p2 = corners[(i+1)%4]
            edge = (p2[0]-p1[0], p2[1]-p1[1])
            normal = (-edge[1], edge[0])
            length = math.hypot(*normal)
            if length != 0:
                normal = (normal[0]/length, normal[1]/length)
            axes.append(normal)
        return axes

    axes = get_axes(corners1) + get_axes(corners2)

    def project(corners, axis):
        dots = [corner[0]*axis[0] + corner[1]*axis[1] for corner in corners]
        return min(dots), max(dots)

    for axis in axes:
        min1, max1 = project(corners1, axis)
        min2, max2 = project(corners2, axis)
        if max1 < min2 or max2 < min1:
            return False
    return True

# OBB 꼭짓점 계산
def get_obb_corners(center, size, angle_deg):
    cx, cy = center
    w, h = size
    rad = math.radians(angle_deg)
    cos_a = math.cos(rad)
    sin_a = math.sin(rad)
    corners = []
    for dx in [-w/2, w/2]:
        for dy in [-h/2, h/2]:
            x = cx + dx * cos_a - dy * sin_a
            y = cy + dx * sin_a + dy * cos_a
            corners.append((x, y))
    return [corners[0], corners[1], corners[3], corners[2]]

running = True
while running:
    clock.tick(60)

    # 이벤트 처리
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 플레이어 이동
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        player_rect.x -= speed
    if keys[pygame.K_RIGHT]:
        player_rect.x += speed
    if keys[pygame.K_UP]:
        player_rect.y -= speed
    if keys[pygame.K_DOWN]:
        player_rect.y += speed

    # OBB 회전 속도
    obb_angle += fast_rotation if keys[pygame.K_z] else rotation_speed
    obb_angle %= 360

    # 🔵 원형 충돌
    player_center = player_rect.center
    center_center = center_pos
    player_radius = player_size // 2
    center_radius = center_size // 2
    dx = player_center[0] - center_center[0]
    dy = player_center[1] - center_center[1]
    distance_sq = dx*dx + dy*dy
    circle_collision = distance_sq < (player_radius + center_radius)**2

    # 🔴 AABB 충돌
    center_rect = pygame.Rect(0, 0, center_size, center_size)
    center_rect.center = center_center
    aabb_collision = player_rect.colliderect(center_rect)

    # 🟢 OBB 충돌 (SAT)
    obb_corners = get_obb_corners(center_center, (center_size, center_size), obb_angle)
    player_corners = get_obb_corners(player_rect.center, (player_size, player_size), 0)
    obb_collision = sat_collision(obb_corners, player_corners)

    # 배경색: OBB > Circle > 기본
    if obb_collision:
        screen.fill(RED)
    elif circle_collision:
        screen.fill(YELLOW)
    else:
        screen.fill(BG)

    # 🔹 본체 + 플레이어
    pygame.draw.rect(screen, GRAY, center_rect)
    pygame.draw.rect(screen, GRAY, player_rect)

    # 🔵 원형 Bounding Box
    pygame.draw.circle(screen, BLUE, player_center, player_radius, 2)
    pygame.draw.circle(screen, BLUE, center_center, center_radius, 2)

    # 🔴 AABB 테두리
    pygame.draw.rect(screen, RED, center_rect, 2)
    pygame.draw.rect(screen, RED, player_rect, 2)

    # 🟢 회전 OBB 테두리
    pygame.draw.polygon(screen, GREEN, obb_corners, 2)

    # 🔹 충돌 상태 표시 (왼쪽 상단)
    texts = [
        f"Circle: {'HIT' if circle_collision else '---'}",
        f"AABB: {'HIT' if aabb_collision else '---'}",
        f"OBB: {'HIT' if obb_collision else '---'}"
    ]
    for i, t in enumerate(texts):
        img = font.render(t, True, WHITE)
        screen.blit(img, (10, 10 + i*30))

    pygame.display.flip()

pygame.quit()
sys.exit()

이 코드에선 닿을떄마다 왼쪽 상단에 각 부분별로 HIT가 표시되었기에 어떻게 닿앗는지를 알 수 있었다

### 원형 vs AABB는 모서리 부분에서 어떤 차이가 있들까?
GPT한테 질문하니 원은 둥글기때문에 모서리 충돌반응이 관대하지만 AABB는 모서리까지 정확하게 체크하기에 훨씬 효과적인것 같았다

### AABB vs OBB: 회전 시 어떤 차이가 있을까?
GPT는 AABB는 정사각형만 표시하기에 정확하지만 불필요하게 큰 범위때문에 실제 충돌보다 일찍 반응이 일어날 수 있다고 하였다. 반면에 OBB는 실제모서리 방향을 확인하기에 회전시 모양에따라 직접 닿았을때에만 충돌반응이 일어났다'

## AI와의 대화에서 배운것

나는 AI에게 AABB란 무엇인지, OBB란 무엇인지를 물어보았고 둘다 정확히 대답해주었다.
나는 충돌방식이 안 나뉘어잇는 줄 알았다. 그러나 충돌방식은 내 생각외로 나뉘어져 잇었고 AABB OBB 무엇을 선택하느냐에 따라 충돌 반응이 달라지는 것을 알 수 잇었다
내가 여태까지 안맞았는데 맞았다고 착각한 게임들이 사실은 AABB를 활용한 게임이 아니었을까?
새로운 질문을 낳기도 하면서 오늘 배운 내용을 기록하였다

## 내 게임에는 어떤 방식이 더 적합할까?

나는 모서리, 회전 정확히 둘다 고려하여 억울한 상황을 만들고 싶지 않기에 OBB방식을 활용하여 보다 정확한 충돌 판정을 활용한 게임을 만들고 싶다. 따라서 나는 OBB방식이 내게임에 더 적합한것 같다






