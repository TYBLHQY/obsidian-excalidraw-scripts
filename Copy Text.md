/*

Copy the text content of selected text elements to the clipboard.

If multiple text elements are selected, their contents will be joined with two newlines.

```javascript
*/
try {
    let elements = ea.getViewSelectedElements().filter(el => el.type === "text");
    if (elements.length === 0) {
        new Notice("❌ No text element selected", 4000);
        return;
    }
    const texts = elements
        .map(el => el.rawText ? el.rawText.trim() : "")
        .filter(text => text.length > 0);
    const finalText = texts.join("\n\n");
    await navigator.clipboard.writeText(finalText);
    new Notice("✅ Text copied to clipboard", 4000);
} catch (err) {
    console.error(err);
    new Notice("❌ Copy failed", 4000);
}
