/**
 * Code.gs
 * Google Apps Script — 狼人殺 (完整版)
 * 儲存：PropertiesService.getScriptProperties().setProperty('WEREWOLF_ROOMS', JSON.stringify(roomsObj))
 *
 * 權限：會使用 DriveApp（儲存頭像）
 */

const CONFIG = {
  ROOM_KEY: 'WEREWOLF_ROOMS',
  AVATAR_FOLDER: 'Werewolf_Avatars',
  POLL_INTERVAL_MS: 1500,
  DEFAULT_PLAYERS: 6,
  ROLE_DISTRIBUTION: ['werewolf','werewolf','seer','doctor','villager','villager'] // for 6 players
};

// ======= 基礎工具 =======
function _loadRooms(){
  const p = PropertiesService.getScriptProperties().getProperty(CONFIG.ROOM_KEY);
  return p ? JSON.parse(p) : {};
}
function _saveRooms(obj){
  PropertiesService.getScriptProperties().setProperty(CONFIG.ROOM_KEY, JSON.stringify(obj));
}
function _now(){ return (new Date()).toISOString(); }
function _uid(prefix){
  return (prefix||'id_') + Math.random().toString(36).slice(2,9);
}

/**
 * 取得或建立 avatar 資料夾
 */
function _getAvatarFolder(){
  const name = CONFIG.AVATAR_FOLDER;
  const it = DriveApp.getFoldersByName(name);
  if(it.hasNext()) return it.next();
  return DriveApp.createFolder(name);
}

/* ===== Web App entry ===== */
function doGet(e){
  return HtmlService.createTemplateFromFile('index').evaluate()
    .setTitle('線上狼人殺（Apps Script）')
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
}

/* ===== 房間 / 玩家 基礎功能 ===== */

/**
 * 建立房間（由房主呼叫）
 * hostName: 字串
 * hostAvatarUrl: 可為空字串
 */
function createRoom(hostName, hostAvatarUrl){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    const rid = 'R' + Math.random().toString(36).slice(2,7).toUpperCase();
    const hostId = _uid('P');
    rooms[rid] = {
      id: rid,
      hostId: hostId,
      createdAt: _now(),
      phase: 'lobby', // lobby -> rolesAssigned -> night -> day -> ended
      round: 0,
      players: {}, // playerId -> { id, name, avatar, alive, role, joinedAt }
      chat: [], // {time, system?, name?, text}
      night: {}, // night actions storage
      votes: {}  // day votes
    };
    rooms[rid].players[hostId] = {
      id: hostId,
      name: hostName || '玩家',
      avatar: hostAvatarUrl || '',
      alive: true,
      role: null,
      joinedAt: Date.now()
    };
    _saveRooms(rooms);
    return { roomId: rid, playerId: hostId };
  } finally {
    lock.releaseLock();
  }
}

/**
 * 加入房間
 */
function joinRoom(roomId, name, avatarUrl){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    if(!rooms[roomId]) return { error: '房間不存在' };
    const room = rooms[roomId];
    const pid = _uid('P');
    room.players[pid] = {
      id: pid,
      name: name || '玩家',
      avatar: avatarUrl || '',
      alive: true,
      role: null,
      joinedAt: Date.now()
    };
    room.chat.push({ time: _now(), system: true, text: `${name} 已加入房間` });
    _saveRooms(rooms);
    return { playerId: pid, room: _stripForClient(room) };
  } finally {
    lock.releaseLock();
  }
}

/**
 * 離開房間（將 player 設為 offline；或完全移除）
 */
function leaveRoom(roomId, playerId){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    if(!rooms[roomId]) return;
    const room = rooms[roomId];
    if(room.players && room.players[playerId]){
      // 我們把玩家移除（若要只是標記 offline 可改）
      const name = room.players[playerId].name;
      delete room.players[playerId];
      room.chat.push({ time:_now(), system:true, text: `${name} 離開房間` });
      _saveRooms(rooms);
    }
  } finally {
    lock.releaseLock();
  }
}

/* ===== 上傳頭像（前端傳 DataURL） =====
   傳入 base64 DataURL 字串與檔名，回傳公開檔案 URL（Drive）
*/
function uploadAvatar(dataUrl, filename){
  if(!dataUrl) return '';
  const matches = dataUrl.match(/^data:(.+);base64,(.+)$/);
  if(!matches) return '';
  const contentType = matches[1];
  const base64 = matches[2];
  const bytes = Utilities.base64Decode(base64);
  const folder = _getAvatarFolder();
  const cleanName = (filename || 'avatar') + '_' + Math.floor(Math.random()*10000);
  const blob = Utilities.newBlob(bytes, contentType, cleanName);
  const file = folder.createFile(blob);
  // 設為 anyone with link 可讀
  try{
    file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);
  }catch(e){
    // 若沒有權限，忽略（通常會有）
  }
  return file.getDownloadUrl ? file.getDownloadUrl() : file.getUrl();
}

/* ===== 取房間狀態（供前端輪詢） ===== */
function getRoomState(roomId, requesterId){
  const rooms = _loadRooms();
  if(!rooms[roomId]) return { error: '房間不存在' };
  const room = rooms[roomId];
  // 回傳對 client 安全且必要的資料（不要把所有角色公開給所有人）
  return _stripForClient(room, requesterId);
}

/**
 * 把房間資料過濾成可以給前端的安全格式
 * requesterId: 若非房主或未被允許，某些欄位會被遮蔽（例如其他人的 role）
 */
function _stripForClient(room, requesterId){
  const r = {
    id: room.id,
    hostId: room.hostId,
    phase: room.phase,
    round: room.round,
    chat: room.chat || [],
    players: {},
    votes: room.votes || {}
  };

  Object.values(room.players || {}).forEach(p=>{
    let showRole = false;

    if(room.phase === 'ended'){
      // 遊戲結束，公開所有角色
      showRole = true;
    } else if(p.id === requesterId){
      // 永遠可以看到自己的角色
      showRole = true;
    }
    // 房主不能看到其他人角色，除非遊戲結束
    // 其他玩家也不能看到別人角色，除非遊戲結束
    r.players[p.id] = {
      id: p.id,
      name: p.name,
      avatar: p.avatar || '',
      alive: !!p.alive,
      role: showRole ? p.role : null
    };
  });

  // 預言家夜晚查看結果仍可單獨看到
  if(requesterId && room.night && room.night.checks && room.night.checks[requesterId]){
    r.myCheck = room.night.checks[requesterId]; // {targetId, role}
  }

  return r;
}

/* ===== 分配身分（房主） ===== */
function assignRoles(roomId, callerId){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    if(!rooms[roomId]) return { error:'房間不存在' };
    const room = rooms[roomId];
    if(room.hostId !== callerId) return { error:'只有房主能分配身分' };
    const players = Object.values(room.players || {});
    const n = players.length;
    if(n < 4) return { error:'玩家不足（至少 4 人）' };
    // 準備身分：若玩家數等於 CONFIG.DEFAULT_PLAYERS，使用 ROLE_DISTRIBUTION，否則自動生成（簡單規則）
    let roles = [];
    if(n === CONFIG.DEFAULT_PLAYERS) roles = CONFIG.ROLE_DISTRIBUTION.slice();
    else {
      // 1狼人 per 3 players (rough), 保證至少1狼人、1預言家、1守衛
      let wolves = Math.max(1, Math.floor(n/3));
      roles = [];
      for(let i=0;i<wolves;i++) roles.push('werewolf');
      roles.push('seer'); roles.push('doctor');
      while(roles.length < n) roles.push('villager');
    }
    // shuffle and assign
    roles = _shuffle(roles);
    const shuffledPlayers = _shuffle(players.map(p=>p.id));
    shuffledPlayers.forEach((pid, i)=>{
      room.players[pid].role = roles[i];
      room.players[pid].alive = true;
    });
    room.phase = 'rolesAssigned';
    room.round = 0;
    room.night = { kills: {}, saves: {}, checks: {} };
    room.chat.push({ time:_now(), system:true, text:'房主分配了身分' });
    _saveRooms(rooms);
    return { ok:true };
  } finally {
    lock.releaseLock();
  }
}

/* ===== 夜晚操作（玩家 client 呼叫） =====
   - werewolf 可 submit kill: night.kills[playerId]=targetId
   - seer 可 submit check: night.checks[playerId]={targetId, role}
   - doctor 可 submit save: night.saves[playerId]=targetId
*/
function submitNightAction(roomId, playerId, action){
  /**
   * action = { type: 'kill'|'check'|'save', targetId: 'Pxxxx' }
   */
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    if(!rooms[roomId]) return { error:'room not found' };
    const room = rooms[roomId];
    const p = room.players[playerId];
    if(!p || !p.alive) return { error:'player invalid' };
    if(action.type === 'kill' && p.role === 'werewolf'){
      room.night.kills = room.night.kills || {};
      room.night.kills[playerId] = action.targetId;
      room.chat.push({ time:_now(), system:true, text: `（狼人） 已選擇目標` });
    } else if(action.type === 'check' && p.role === 'seer'){
      room.night.checks = room.night.checks || {};
      const t = room.players[action.targetId];
      room.night.checks[playerId] = { targetId: action.targetId, role: t? t.role : null };
      room.chat.push({ time:_now(), system:true, text: `（預言家） 已查看某位玩家` });
    } else if(action.type === 'save' && p.role === 'doctor'){
      room.night.saves = room.night.saves || {};
      room.night.saves[playerId] = action.targetId;
      room.chat.push({ time:_now(), system:true, text: `（守衛） 已選擇保護` });
    } else {
      return { error:'action not permitted' };
    }
    _saveRooms(rooms);
    return { ok:true };
  } finally {
    lock.releaseLock();
  }
}

/* ===== 房主執行：結束夜晚並計算結果 ===== */
function resolveNight(roomId, callerId){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    const room = rooms[roomId];
    if(!room) return { error:'room not found' };
    if(room.hostId !== callerId) return { error:'only host can resolve night' };
    // 判斷 kill: 多個狼人可能有多個選擇，這裡簡化成：統計被選最多的 target
    const kills = room.night.kills || {};
    const saves = room.night.saves || {};
    const checks = room.night.checks || {};
    const tally = {};
    Object.values(kills).forEach(tid=>{ tally[tid] = (tally[tid]||0)+1; });
    let targetId = null;
    if(Object.keys(tally).length>0){
      // pick max voters
      targetId = Object.entries(tally).sort((a,b)=>b[1]-a[1])[0][0];
    }
    // 決定是否被守衛救下（若有多個守衛選擇，任何一個救下都有效）
    let saved = false;
    if(targetId){
      const saveTargets = Object.values(saves || {});
      if(saveTargets.indexOf(targetId) !== -1) saved = true;
    }
    let killedName = null;
    if(targetId && !saved){
      if(room.players[targetId]){
        room.players[targetId].alive = false;
        killedName = room.players[targetId].name;
        room.chat.push({ time:_now(), system:true, text: `夜晚結果：${killedName} 遭到攻擊並死亡` });
      }
    } else {
      if(targetId && saved){
        room.chat.push({ time:_now(), system:true, text: `夜晚結果：有人遭到攻擊，但被守衛救下` });
      } else {
        room.chat.push({ time:_now(), system:true, text: `夜晚結果：沒有攻擊發生` });
      }
    }
    // 把預言家的查看結果放在 room.night.reports 以供其查看
    room.night.reports = room.night.reports || {};
    Object.entries(checks || {}).forEach(([seerId,info])=>{
      room.night.reports[seerId] = info; // store {targetId, role}
    });
    // 清空夜晚行動（或保留歷史視需求）
    room.night.kills = {}; room.night.saves = {}; room.night.checks = {};
    // 進入白天
    room.phase = 'day';
    room.round = (room.round || 0) + 1;
    room.votes = {}; // reset votes
    // 檢查勝利
    _evaluateWin(room);
    _saveRooms(rooms);
    return { ok:true, killed: killedName || null };
  } finally {
    lock.releaseLock();
  }
}

/* ===== 白天投票：每位玩家呼叫 submitVote() 寫入自己的投票 ===== */
function submitVote(roomId, voterId, targetId){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    const room = rooms[roomId];
    if(!room) return { error:'room not found' };
    room.votes = room.votes || {};
    room.votes[voterId] = targetId;
    _saveRooms(rooms);
    return { ok:true };
  } finally {
    lock.releaseLock();
  }
}

/* ===== 房主計算投票結果並執行處決 ===== */
function resolveVotes(roomId, callerId){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    const room = rooms[roomId];
    if(!room) return { error:'room not found' };
    if(room.hostId !== callerId) return { error:'only host can resolve votes' };
    const votes = room.votes || {};
    const tally = {};
    Object.values(votes).forEach(tgt=>{ if(tgt) tally[tgt] = (tally[tgt]||0) + 1; });
    if(Object.keys(tally).length === 0){
      room.chat.push({ time:_now(), system:true, text: '投票無效：沒有人被投票' });
    } else {
      const victimId = Object.entries(tally).sort((a,b)=>b[1]-a[1])[0][0];
      if(room.players[victimId]){
        room.players[victimId].alive = false;
        room.chat.push({ time:_now(), system:true, text: `投票結果：${room.players[victimId].name} 被處決` });
      }
    }
    room.votes = {};
    // 回到夜晚（除非遊戲結束）
    _evaluateWin(room);
    if(room.phase !== 'ended') room.phase = 'night';
    _saveRooms(rooms);
    return { ok:true };
  } finally {
    lock.releaseLock();
  }
}

/* ===== 勝利判定（簡單版） ===== */
function _evaluateWin(room){
  const players = Object.values(room.players || {});
  const wolvesAlive = players.filter(p=>p.alive && p.role === 'werewolf').length;
  const othersAlive = players.filter(p=>p.alive && p.role !== 'werewolf').length;
  if(wolvesAlive === 0){
    room.phase = 'ended';
    room.winner = 'villagers';
    room.chat.push({ time:_now(), system:true, text: '遊戲結束：村民勝利' });
  } else if(wolvesAlive >= othersAlive){
    room.phase = 'ended';
    room.winner = 'werewolves';
    room.chat.push({ time:_now(), system:true, text: '遊戲結束：狼人勝利' });
  }
}

/* ===== 聊天（push message 至房間 chat） ===== */
function postChat(roomId, playerId, text){
  const lock = LockService.getScriptLock();
  lock.waitLock(30000);
  try{
    const rooms = _loadRooms();
    if(!rooms[roomId]) return { error:'room not found' };
    const room = rooms[roomId];
    const name = room.players[playerId] ? room.players[playerId].name : '匿名';
    room.chat.push({ time:_now(), name: name, text: text });
    _saveRooms(rooms);
    return { ok:true };
  } finally {
    lock.releaseLock();
  }
}

/* ===== 工具：洗牌 ===== */
function _shuffle(arr){
  for(let i=arr.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]]; }
  return arr;
}
