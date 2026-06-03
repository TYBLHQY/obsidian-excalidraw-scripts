/*

Auto Sentence Numbering for Excalidraw
This script takes the selected text elements in Excalidraw, splits their content into sentences based on common punctuation marks, and then prefixes each sentence with a circled number (①, ②, etc.) to indicate its order. If a text element contains only one sentence, it will simply be prefixed with "①". The script handles up to 50 sentences per text element, after which it defaults to using regular numbering (e.g., "51."). After processing, the updated text elements are added back to the view, and a notice is displayed indicating how many text elements were processed.

```javascript
*/

let selected = ea.getViewSelectedElements().filter(el => el.type === "text");

if (selected.length === 0) {
    new Notice("❌ No text elements selected", 4000);
    return;
}

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

function isAbbreviation(text, index) {
    const window = text
        .replace(/\n/g, " ")
        .slice(Math.max(0, index - 20), index + 20);

    return (
        /(U\.S\.|U\.K\.|U\.N\.|E\.U\.|U\.S\.A\.|P\.R\.C\.)/.test(window) ||
        /(Ph\.D\.|M\.D\.|D\.D\.S\.|J\.D\.|M\.A\.|B\.A\.|B\.S\.|M\.S\.|Esq\.)/.test(window) ||
        /\b(Dr|Mr|Mrs|Ms|Miss|Prof|Rev|Hon|Gen|Col|Lt|Capt|Maj|Sgt|Rep|Sen|Gov|Jr|Sr)\./.test(window) ||
        /\b(e\.g\.|i\.e\.|etc\.|cf\.|viz\.|ibid\.|ca\.|approx\.)/.test(window) ||
        /(Jan\.|Feb\.|Mar\.|Apr\.|May|Jun\.|Jul\.|Aug\.|Sep\.|Oct\.|Nov\.|Dec\.)/.test(window) ||
        /[ap]\.m\./i.test(window) ||
        /(Inc\.|Ltd\.|Co\.|Corp\.|LLC\.|Assn\.|Bros\.|Dept\.|Univ\.)/.test(window) ||
        /(Rd\.|Ave\.|Blvd\.|Ln\.|St\.|Ct\.|Pl\.|Dr\.|Ter\.|Hwy\.)/.test(window) ||
        /\bvs\.?/i.test(window)
    );
}

function addRawNumbers(text) {
    let count = 1;
    text = text.trim();
    let result = `① `;
    const regex = /([。！？.!?]+(?:["“”"'』」》】）])?)(\s*)/g;
    result += text.replace(regex, (match, punctuation, spaces, offset, fullText) => {
        if (spaces.includes("\n") || isAbbreviation(fullText, offset)) return match;
        count++;
        return `${punctuation}${spaces}${getCircledNumber(count)} `;
    });
    return result;
}

function removeTrailingNumber(text) {
    return text.replace(/\s*[①-㊿]\s*$/, "");
}

function addSentenceNumbers(text) {
    return removeTrailingNumber(addRawNumbers(text));
}

let processedCount = 0;

ea.copyViewElementsToEAforEditing(selected);

const elementsInEA = ea.getElements().filter(el => el.type === "text");

for (let el of elementsInEA) {
    const originalText = (el.rawText || el.text || "").trim();
    if (!originalText) continue;

    let numberedText = addSentenceNumbers(originalText);

    el.rawText = numberedText;
    if (el.text) el.text = numberedText;

    processedCount++;
}

await ea.addElementsToView(false, false, true);

new Notice(`✅ Processed ${processedCount} text elements`, 5000);
