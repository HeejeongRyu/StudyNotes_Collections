from pathlib import Path

# JavaScript content protection Markdown content
js_protection_md = """
# 📘 JavaScript 캡처 방지, 우클릭 금지, 워터마크 삽입

웹 페이지의 콘텐츠 보호를 위한 JavaScript 기술들을 정리했습니다.  
완벽한 보안은 아니지만, 일반 사용자의 무단 복제/캡처를 어렵게 만드는 데 도움이 됩니다.

---

## ✅ 1. 우클릭 방지

```html
<script>
  document.addEventListener("contextmenu", function (e) {
    e.preventDefault();
    alert("우클릭은 사용할 수 없습니다.");
  });
</script>
✅ 2. 드래그 선택/복사 방지
html
항상 세부 정보 표시

코드 복사
<style>
  body {
    user-select: none;
    -webkit-user-drag: none;
  }
</style>

<script>
  document.addEventListener("copy", (e) => {
    e.preventDefault();
    alert("복사 금지되어 있습니다.");
  });
</script>
✅ 3. 키보드 캡처 방지
html
항상 세부 정보 표시

코드 복사
<script>
  document.addEventListener("keydown", function (e) {
    if ((e.ctrlKey && e.key === "u") ||
        (e.ctrlKey && e.key === "c") ||
        (e.key === "PrintScreen")) {
      e.preventDefault();
      alert("허용되지 않은 키입니다.");
    }
  });
</script>
✅ 4. 워터마크 추가 (고정 위치 텍스트)
html
항상 세부 정보 표시

코드 복사
<style>
  .watermark {
    position: fixed;
    bottom: 20px;
    right: 20px;
    opacity: 0.2;
    font-size: 14px;
    z-index: 9999;
    pointer-events: none;
  }
</style>

<div class="watermark">© Heejeong Ryu - Do not copy</div>
✅ 5. 클립보드 초기화
html
항상 세부 정보 표시

코드 복사
<script>
  setInterval(() => {
    if (navigator.clipboard) {
      navigator.clipboard.writeText("");
    }
  }, 1000);
</script>
⚠️ 유의사항
위 기법들은 일반 사용자를 대상으로 한 방어책일 뿐

개발자 도구(F12), 스크린 캡처 툴 등은 우회 가능

백엔드 인증, 워터마킹 병행이 필요함

📌 요약
기능	설명
우클릭 방지	contextmenu 이벤트 차단
복사/드래그 방지	user-select, copy 이벤트 차단
키보드 방지	Ctrl+U, PrintScreen 차단
워터마크 삽입	고정 위치 텍스트
클립보드 초기화	클립보드 내용 주기적 삭제

🎯 콘텐츠 보호의 보조 수단으로 활용하며, 완전한 방지는 서버 측 조치와 함께해야 합니다.
"""

Save to file
js_protection_path = Path("/mnt/data/js_copy_protection.md")
js_protection_path.write_text(js_protection_md, encoding="utf-8")

js_protection_path.name