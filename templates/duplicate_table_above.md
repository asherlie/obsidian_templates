<%*
const file = tp.file.content;
let lines = file.split("\n");
// NOTE: buttons should have attribute "remove true"
// TODO: add timestamp on creation somewhere in table
function transform_ln(table_ln, ln) {
	let sep_ln = "";
	let ret = "";
	if (table_ln < 2) {
		return ln
	}
	sep_ln = ln.split("|");
	ret = "|" + sep_ln[1];
	for (let i = 2; i < sep_ln.length; ++i) {
		if (sep_ln[i] == "") {
			ret += "|";
		}
	}
	// console.log(ret);
	return ret;
	//return ln.split("|")[0];
}

// using button line as starting point for search
// because we can't 
let cursor_ln = 0;
let table_lines = [];
let button_start_ln = 0;
let button_end_ln = 0;
let button_text = ""

// find text of button markdown as start point
for (let i = 0; i < lines.length; ++i) {
	if (lines[i].includes("duplicate benching table")) {
		button_text = lines.slice(i-1, i+5).join("\n");
		console.log(button_text);
		button_start_ln = i-1;
		button_end_ln = i+4;
		cursor_ln = i;
		break;
	}
}

/* find the nearest table above
okay, use a diff variable to iterate forwards from the top of the file instead of bottom.
this way we can keep the entire first line as the header and keep only the first column of each additional line
*/
let table_ln = 0;
// ah, just realized that we're always copying the first table instead of last. need to move backwards from button
// nvm, let's just do a backwards search for the header from the button and move forward from there
// "| ------ | ---- | ---------------------------------- |"
// i can prob just do 
// .split("|").join("").split("-").join("").split(" ").join("")

// yes!! that's awesome. check if the above is "", if so we're one away from the start
let table_start_ln = 0;
// we're finding the line that our relevant table begins at
for (let i = cursor_ln; i >= 0; --i) {
	// if line is made up of only header divider chars
	if (lines[i].split("|").join("").split("-").join("").split(" ").join("") == "") {
		table_start_ln = i - 1;
		console.log("table starts at:");
		console.log(i - 1);
		break;
	}
}
/*
for (let i = cursor_ln; i >= 0; --i) {
    if (lines[i].startsWith("|")) {
		// the passing of i is the only broken thing here - need to pass in line number somehow. should be len - i, but we don't know table line len yet
        table_lines.unshift(transform_ln(table_ln, lines[i]));
		++table_ln;
	} else if (table_lines.length > 0) {
		break;
	}
}
*/
for (let i = table_start_ln; i < cursor_ln; ++i) {
    if (lines[i].startsWith("|")) {
        table_lines.push(transform_ln(table_ln, lines[i]));
		++table_ln;
	} else if (table_lines.length > 0) {
		break;
	}
}
// insert the found table below the button
if (table_lines.length > 0) {
    const table_text = table_lines.join("\n");
	// don't forget to add a new button
	// console.log(tp.date.now("MM-DD-YYYY").length);
	tR += "\n`" + tp.date.now("MM-DD-YYYY") + ":`\n";
    tR += "\n" + table_text + "\n" + button_text;
	//lines.splice(button_start_ln, 3);
	//console.log(lines);
	//await tp.file.write(lines.join("\n"));
	
	//fp.file.content.splice(button_);
} else {
    // tR += "\n❌ No table found above.\n";
}
%>