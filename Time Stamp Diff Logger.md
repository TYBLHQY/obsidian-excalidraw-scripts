/*

Log the time difference between the current time and a selected text element containing a timestamp.

If no text element is selected, a new text element with the current timestamp will be created.

If a text element is selected and contains a valid timestamp in the format "YYYY-MM-DD HH:MM:SS",
a new text element will be created at the same position with the original timestamp,
the current timestamp, and the duration in minutes between the two timestamps.

If the selected text element does not contain a valid timestamp, no action will be taken.

The new text elements will use the "Code" font style for better readability.

```javascript
*/
const now = new Date();

const formatDate = (date) => {
  const y = date.getFullYear();
  const m = String(date.getMonth() + 1).padStart(2, '0');
  const d = String(date.getDate()).padStart(2, '0');
  const h = String(date.getHours()).padStart(2, '0');
  const min = String(date.getMinutes()).padStart(2, '0');
  const s = String(date.getSeconds()).padStart(2, '0');
  return `${y}-${m}-${d} ${h}:${min}:${s}`;
};

const currentTimeStr = formatDate(now);
let selected = ea.getViewSelectedElements().filter(el => el.type === "text");

if (selected.length === 0) {
  ea.style.fontFamily = 8;
  ea.style.fontSize = 20;
  ea.addText(400, 300, currentTimeStr);
  await ea.addElementsToView(true, true, true);
  new Notice("✅ Timestamp added", 4000);
  return;
}

const el = selected[0];
const timeRegex = /^\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}$/;
const isValidTime = timeRegex.test(el.rawText.trim());

if (isValidTime) {
  const startTime = new Date(el.rawText.trim());
  const diffMs = now - startTime;
  const diffMinutes = Math.round(diffMs / 60000);
  
  const newText = `${el.rawText.trim()}\n${currentTimeStr}\nDuration: ${diffMinutes} minutes`;
  
  ea.style.strokeColor = el.strokeColor;
  ea.style.fontFamily = 8;
  ea.style.fontSize = el.fontSize || 20;
  
  ea.addText(el.x, el.y, newText);
  await ea.addElementsToView(false, false, true);
  ea.deleteViewElements([el]);
  new Notice("✅ Time difference logged", 4000);
  return;
}

new Notice("❌ Selected text does not contain a valid timestamp (YYYY-MM-DD HH:MM:SS)", 4000);

