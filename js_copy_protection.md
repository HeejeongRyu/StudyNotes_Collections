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
