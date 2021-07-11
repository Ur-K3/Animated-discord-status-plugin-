# Animated-discord-status-plugin-

		actions.appendChild(actionsRich);

		// Move save to the right
		actions.appendChild(GUI.setExpand(document.createElement("div"), 2));

		let save = GUI.newButton("Save");
		GUI.setSuggested(save, true);
		actions.appendChild(save);
		save.onclick = () => {
			try {
				// Set timeout
				this.setData("timeout", parseInt(timeout.value));
				this.setData("editor", this.editor);

				// Set Animation
				if (this.editor == Editor_Type.RICH)
					this.setData("animation", this.richEditToJson(edit));
				else
					this.setData("animation", this.strToJson(edit.value));
			}
			catch (e) {
				BdApi.showToast(e, {type: "error"});
				return;
			}

			// Show Toast
			BdApi.showToast("Settings were saved!", {type: "success"});

			// Restart
			this.load();
			this.stop();
			this.start();
		};

		// End
		return settings;
	}
}

/* Status API */
const Status = {
	authToken: Object.values(webpackJsonp.push([ [], { ['']: (_, e, r) => { e.cache = r.c } }, [ [''] ] ]).cache).find(m => m.exports && m.exports.default && m.exports.default.getToken !== void 0).exports.default.getToken(),

	strerror: (req) => {
		if (req.status < 400)
			return undefined;

		if (req.status == 401)
			return "Invalid AuthToken";

		let json = JSON.parse(req.response);
		for (const s of ["errors", "custom_status", "text", "_errors", 0, "message"])
			if ((json == undefined) || ((json = json[s]) == undefined))
				return "Internal. Report at github.com/toluschr/BetterDiscord-Animated-Status";

		return json;
	},

	request: () => {
		let req = new XMLHttpRequest();
		req.open("PATCH", "/api/v8/users/@me/settings", true);
		req.setRequestHeader("authorization", Status.authToken);
		req.setRequestHeader("content-type", "application/json");
		req.onload = () => {
			let err = Status.strerror(req);
			if (err != undefined)
				BdApi.showToast(`Animated Status: Error: ${err}`, {type: "error"});
		};
		return req;
	},

	set: (status) => {
		let data = {};

		if (status.length == 0) return;
		if (status.length >= 1) data.text = status[0];
		if (status.length >= 2) data.emoji_name = status[1];
		if (status.length >= 3) data.emoji_id = status[2];

		Status.request().send(JSON.stringify({custom_status: data}));
	},

	unset: () => {
		Status.request().send('{"custom_status":null}');
	}
};

// Used to easily style elements like the 'native' discord ones
const GUI = {
	newInput: (text = "") => {
		let input = document.createElement("input");
		input.className = "inputDefault-_djjkz input-cIJ7To";
		input.innerText = text;
		return input;
	},

	newLabel: (text) => {
		let label = document.createElement("h5");
		label.className = "h5-18_1nd";
		label.innerText = text;
		return label;
	},

	newTextarea: () => {
		let textarea = document.createElement("textarea");
		textarea.className = "input-cIJ7To scrollbarGhostHairline-1mSOM1";
		textarea.style.resize = "vertical";
		textarea.rows = 4;
		return textarea;
	},

	newButton: (text, filled = true) => {
		let button = document.createElement("button");
		button.className = "button-38aScr colorBrand-3pXr91 sizeSmall-2cSMqn grow-q77ONN";
		if (filled) button.classList.add("lookFilled-1Gx00P");
		else button.classList.add("lookOutlined-3sRXeN");
		button.innerText = text;
		return button;
	},

	newHBox: () => {
		let hbox = document.createElement("div");
		hbox.style.display = "flex";
		hbox.style.flexDirection = "row";
		return hbox;
	},

	setExpand: (element, value) => {
		element.style.flexGrow = value;
		return element;
	},

	setSuggested: (element, value = true) => {
		if (value) element.classList.add("colorGreen-29iAKY");
		else element.classList.remove("mystyle");
		return element;
	},

	setDestructive: (element, value = true) => {
		if (value) element.classList.add("colorRed-1TFJan");
		else element.classList.remove("colorRed-1TFJan");
		return element;
	}
};
