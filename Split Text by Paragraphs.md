/*

Split the text content of selected text elements into multiple text elements,
one for each paragraph (split by double newlines).

The new text elements will be styled the same as the original and will be positioned vertically below the original element.

The original element will be deleted.

```javascript
*/
elements = ea.getViewSelectedElements().filter(el => el.type === "text");

elements.forEach(el => {
  ea.style.strokeColor = el.strokeColor;
  ea.style.fontFamily = el.fontFamily;
  ea.style.fontSize = el.fontSize;
  const text = el.rawText.split("\n\n");
  for(let i = 0;i < text.length; i++)
	  ea.addText(el.x, el.y + i * el.height / text.length, text[i].trim());
});

await ea.addElementsToView(false, false, true);
ea.deleteViewElements(elements);
new Notice("✅ Text split into paragraphs", 4000);
