Copy the code below:

```
// waypoint mod by KingCobraTheGreat
waypoint = {}
waypointPos = {}
waypointColor = {}
settings = {
	coords: {}
}

onPlayerJoin = (pId) => {
	waypoint[pId] = []
	waypointPos[pId] = []
	waypointColor[pId] = []
	settings.coords[pId] = false
	api.sendMessage(pId, "Running waypoint mod by KingCobraTheGreat\nUse /waypoint to set and delete waypoints")
}

playerCommand = (pId, command) => {
	cmd = command.split(" ")
	if (!cmd[0] || cmd[0] !== "waypoint") return
	if (cmd[1] == "set") {
		if (!cmd[2]) {
			api.sendMessage(pId, "Usage: /waypoint set [name] (colour)\nWaypoint mod by KingCobraTheGreat")
			return true
		}
		if (waypoint[pId].includes(cmd[2])) {
			api.sendMessage(pId, `You already have a waypoint called ${cmd[2]}. Delete it first.\nWaypoint mod by KingCobraTheGreat`)
			return true
		}
		const pos = api.getPosition(pId)
		for (let i = 0; i < pos.length; i++) {
			pos[i] *= 10
			pos[i] = Math.floor(pos[i])
			pos[i] /= 10
		}
		api.setDirectionArrow(pId, cmd[2], pos, settings.coords[pId]?`${cmd[2]} [${pos[0]} ${pos[1]} ${pos[2]}]`:cmd[2], true, {color:cmd[3]?cmd[3]:"white"})
		api.sendMessage(pId, [`Set waypoint ${cmd[2]}`,settings.coords[pId]?` at ${pos[0]} ${pos[1]} ${pos[2]}`:"","\nWaypoint mod by KingCobraTheGreat"])
		waypoint[pId].push(cmd[2])
		waypointPos[pId].push(pos)
		waypointColor[pId].push(cmd[3]?cmd[3]:"white")
		return true
	} else if (cmd[1] == "delete") {
		if (!cmd[2]) {
			api.sendMessage(pId, "Usage: /waypoint delete [name]\nWaypoint mod by KingCobraTheGreat")
			return true
		}
		if (!waypoint[pId].includes(cmd[2])) {
			api.sendMessage(pId, `${cmd[2]} isn't a waypoint you have set.\nWaypoint mod by KingCobraTheGreat`)
			return true
		}
		api.clearDirectionArrow(pId, cmd[2])
		api.sendMessage(pId, `Deleted waypoint ${cmd[2]}.\nWaypoint mod by KingCobraTheGreat`)
		for (i = 0; i < waypoint[pId].length; i++) {
			if (waypoint[pId][i] == cmd[2]) {
				waypoint[pId].splice(i, 1)
				waypointPos[pId].splice(i, 1)
				waypointColor[pId].splice(i, 1)
			}
		}
		return true
	} else if (cmd[1] == "settings") {
		if (!cmd[2]) {
			api.sendMessage(pId, "Usage: /waypoint settings [setting] [value]\nUse \'/waypoint settings help\' for available settings.\nWaypoint mod by KingCobraTheGreat")
		} else if (cmd[2] == "help") {
			api.sendMessage(pId, "Available settings:\ncoords [yes/no] - show coords in waypoints\nWaypoint mod by KingCobraTheGreat")
		} else if (cmd[2] == "coords") {
			if (!cmd[3]) {
				let c = !settings.coords[pId]
				settings.coords[pId] = c
				api.sendMessage(pId, ["Coords are now ",{str:c?"shown":"hidden",style:{color:c?"lime":"red"}},"\nWaypoint mod by KingCobraTheGreat"])
			} else if (cmd[3] == "yes") {
				settings.coords[pId] = true
				api.sendMessage(pId, ["Coords are now ",{str:"shown",style:{color:"lime"}},"\nWaypoint mod by KingCobraTheGreat"])
			} else if (cmd[3] == "no") {
				settings.coords[pId] = false
				api.sendMessage(pId, ["Coords are now ",{str:"hidden",style:{color:"red"}},"\nWaypoint mod by KingCobraTheGreat"])
			} else {
				api.sendMessage(pId, `Currently ${settings.coords[pId]?"shown":"hidden"}. Valid values are yes/no. Leave blank to toggle.\nWaypoint mod by KingCobraTheGreat`)
				return true
			}
			for (i = 0; i < waypoint[pId].length; i++) {
				let pos = waypointPos[pId][i]
				api.clearDirectionArrow(pId, waypoint[pId][i])
				api.setDirectionArrow(pId, waypoint[pId][i], pos, settings.coords[pId]?`${waypoint[pId][i]} [${pos[0]} ${pos[1]} ${pos[2]}]`:waypoint[pId][i], true, {color:waypointColor[pId][i]})
			}
			return true
		} else {
			api.sendMessage(pId, "That setting doesn't exist.\nWaypoint mod by KingCobraTheGreat")
		}
		return true
	} else {
		api.sendMessage(pId, "Use /waypoint [set/delete/settings]\nWaypoint mod by KingCobraTheGreat")
		return true
	}
}

onPlayerKilledOtherPlayer = (pId, hitId, dmg, item) => {
	api.setDirectionArrow(hitId, "death", api.getPosition(hitId), "death", true, {color:"red"})
	waypoint[hitId].push("death")
}
