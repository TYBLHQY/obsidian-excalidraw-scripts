## Copy Text

Copy the text content of selected text elements to the clipboard.

If multiple text elements are selected, their contents will be joined with two newlines.

## Split Text by Paragraphs

Split the text content of selected text elements into multiple text elements,
one for each paragraph (split by double newlines).

The new text elements will be styled the same as the original and will be positioned vertically below the original element.

The original element will be deleted.

## Time Stamp Diff Logger

Log the time difference between the current time and a selected text element containing a timestamp.

If no text element is selected, a new text element with the current timestamp will be created.

If a text element is selected and contains a valid timestamp in the format "YYYY-MM-DD HH:MM:SS",
a new text element will be created at the same position with the original timestamp,
the current timestamp, and the duration in minutes between the two timestamps.

If the selected text element does not contain a valid timestamp, no action will be taken.

The new text elements will use the "Code" font style for better readability.

## Word Counter

This script counts the number of words in the selected text elements in Excalidraw. It filters out non-text elements, combines the text, and then splits it into words while removing punctuation. Finally, it displays the word count in a notice.

## Auto Sentence Numbering

This script automatically adds circled numbers at the beginning of sentences in selected text elements. It intelligently detects sentence boundaries while avoiding common pitfalls such as abbreviations, decimal numbers, and quoted text.
