/*

This script counts the number of words in the selected text elements in Excalidraw. It filters out non-text elements, combines the text, and then splits it into words while removing punctuation. Finally, it displays the word count in a notice.

```javascript
*/
const elements = ea.getViewSelectedElements().filter(el => el.type === "text");

if (elements.length === 0) {
    new Notice("❌ No text element selected", 4000);
    return;
}

const texts = elements
    .map(el => (el.rawText ? el.rawText.trim() : ""))
    .filter(t => t.length > 0);

const combined = texts.join(" ");

const words = combined
    .split(/\s+/)
    .map(w => w.replace(/[^\p{L}\p{N}_'-]/gu, "")) // keep letters/numbers
    .filter(w => w.length > 0);

const count = words.length;

new Notice(`📊 Word count: ${count}`, 5000);
