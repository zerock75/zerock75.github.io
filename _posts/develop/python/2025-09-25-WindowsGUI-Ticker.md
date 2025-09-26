


# 윈도우에서 시각화(응용프로그램)

1. Ticker(표준 라이브러리)
2. PyQT / PySide
 - 전문적인 GUI 라이브러리
3. Kivy
 - 멀티플랫폼 (모바일/데스크탑) 지원
 - 디자인 자유도 높음

## Tkinter

### 기본창
```py
import tkinter as tk

# 윈도우창 만들기
root = tk.Tk()  # 객체생성
root.title("내 첫 GUI")
root.geometry("800x600")

# 위젯 추가
label = tk.Label(root, text="헬로우")
label.pack()

# 버튼 만들고 동작 연결
def on_click():
  label.config(text="버튼 누름!")

button = tk.Button(root, text="버튼이당", command=on_click)
button.pack()


# GUI 실행 루프
root.mainloop()
```

### 파일 선택
```py
from tkinter import filedialog, messagebox
