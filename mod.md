Copy the code below:

```
// waypoint mod by KingCobraTheGreat
waypoint = {}

onPlayerJoin = (pId) => {
	waypoint[pId] = []
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
		api.setDirectionArrow(pId, cmd[2], pos, cmd[2], true, {color:cmd[3]?cmd[3]:"white"})
		api.sendMessage(pId, `Set waypoint ${cmd[2]} at ${pos[0]} ${pos[1]} ${pos[2]}\nWaypoint mod by KingCobraTheGreat`)
		waypoint[pId].push(cmd[2])
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
			if (waypoint[pId][i] == cmd[2]) waypoint[pId].splice(i, 1)
		}
		return true
	} else {
		api.sendMessage(pId, "Use /waypoint [set/delete] [name] (colour)\nWaypoint mod by KingCobraTheGreat")
		return true
	}
}

onPlayerKilledOtherPlayer = (pId, hitId, dmg, item) => {
	api.setDirectionArrow(hitId, "death", api.getPosition(hitId), "death", true, {color:"red"})
	waypoint[hitId].push("death")
}
