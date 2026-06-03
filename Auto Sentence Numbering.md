/*

Auto Sentence Numbering for Excalidraw
This script takes the selected text elements in Excalidraw, splits their content into sentences based on common punctuation marks, and then prefixes each sentence with a circled number (①, ②, etc.) to indicate its order. If a text element contains only one sentence, it will simply be prefixed with "①". The script handles up to 50 sentences per text element, after which it defaults to using regular numbering (e.g., "51."). After processing, the updated text elements are added back to the view, and a notice is displayed indicating how many text elements were processed.

```javascript
*/

const circledNumbers = [
    '①','②','③','④','⑤','⑥','⑦','⑧','⑨','⑩',
    '⑪','⑫','⑬','⑭','⑮','⑯','⑰','⑱','⑲','⑳',
    '㉑','㉒','㉓','㉔','㉕','㉖','㉗','㉘','㉙','㉚',
    '㉛','㉜','㉝','㉞','㉟','㊱','㊲','㊳','㊴','㊵',
    '㊶','㊷','㊸','㊹','㊺','㊻','㊼','㊽','㊾','㊿'
];

function getCircledNumber(n) {
    return n >= 1 && n <= 50 ? circledNumbers[n - 1] : `${n}.`;
}

function addSentenceNumbers(text) {
    let count = 1;
    text = text.trim();
    let result = `① `;
    const regex = /([。！？.!?]+(?:["“”"'』」》】）])?)(\s*)/g;
    result += text.replace(
        regex,
        (match, punctuation, spaces) => {
            count++;
            return `${punctuation}${spaces}${getCircledNumber(count)} `;
        }
    );

    return result;
}

let selected = ea.getViewSelectedElements().filter(el => el.type === "text");

if (selected.length === 0) {
    new Notice("❌ No text elements selected", 4000);
    return;
}

let processedCount = 0;

ea.copyViewElementsToEAforEditing(selected);

const elementsInEA = ea.getElements().filter(el => el.type === "text");

for (let el of elementsInEA) {
    const originalText = (el.rawText || el.text || "").trim();
    if (!originalText) continue;

    let numberedText = addSentenceNumbers(originalText);
    numberedText = numberedText.replace(/(\s*[①-㊿])\s*$/, "");

    el.rawText = numberedText;
    if (el.text) el.text = numberedText;

    processedCount++;
}

await ea.addElementsToView(false, false, true);

new Notice(`✅ Processed ${processedCount} text elements`, 5000);
