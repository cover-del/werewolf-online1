<script>
/* 前端互動與 Polling（輪詢） */
const POLL_INTERVAL = 1500; // ms

let state = {
  roomId: null,
  playerId: null,
  name: null,
  avatarUrl: null,
  isHost: false
};

/* 元素 */
const nameEl = document.getElementById('name');
const avatarFileEl = document.getElementById('avatarFile');
const createBtn = document.getElementById('createBtn');
const joinBtn = document.getElementById('joinBtn');
const roomInput = document.getElementById('roomInput');

const roomPanel = document.getElementById('roomPanel');
const roomLabel = document.getElementById('roomLabel');
const playersWrap = document.getElementById('players');
const chatBox = document.getElementById('chatBox');
const chatInput = document.getElementById('chatInput');
const sendChatBtn = document.getElementById('sendChatBtn');
const assignBtn = document.getElementById('assignBtn');
const resolveNightBtn = document.getElementById('resolveNightBtn');
const resolveVotesBtn = document.getElementById('resolveVotesBtn');
const leaveBtn = document.getElementById('leaveBtn');
const gameStateEl = document.getElementById('gameState');
const actionArea = document.getElementById('actionArea');

/* 事件 */
createBtn.addEventListener('click', async ()=>{
  const name = (nameEl.value || '').trim() || '玩家';
  const avatarBase64 = await _readAvatar();
  createBtn.disabled = true;
  google.script.run.withSuccessHandler(res=>{
    createBtn.disabled = false;
    if(res && res.roomId){
      state.roomId = res.roomId;
      state.playerId = res.playerId;
      state.name = name;
      state.isHost = true;
      _enterRoomUI();
      _startPolling();
    } else {
      alert('建立房間失敗');
    }
  }).createRoom(name, avatarBase64);
});

joinBtn.addEventListener('click', async ()=>{
  const rid = (roomInput.value||'').trim();
  if(!rid){ alert('請輸入房間 ID'); return; }
  const name = (nameEl.value || '').trim() || '玩家';
  const avatarBase64 = await _readAvatar();
  joinBtn.disabled = true;
  google.script.run.withSuccessHandler(res=>{
    joinBtn.disabled = false;
    if(res && res.error){ alert(res.error); return; }
    state.roomId = rid;
    state.playerId = res.playerId;
    state.name = name;
    state.isHost = false;
    _enterRoomUI();
    _startPolling();
  }).joinRoom(rid, name, avatarBase64);
});

sendChatBtn.addEventListener('click', ()=>{
  const txt = (chatInput.value||'').trim();
  if(!txt) return;
  google.script.run.withSuccessHandler(()=>{ chatInput.value=''; }).postChat(state.roomId, state.playerId, txt);
});

/* 房主操作 */
assignBtn.addEventListener('click', ()=>{
  if(!state.isHost) return alert('只有房主可分配身分');
  assignBtn.disabled = true;
  google.script.run.withSuccessHandler(res=>{
    assignBtn.disabled = false;
    if(res && res.error) alert(res.error);
  }).assignRoles(state.roomId, state.playerId);
});
resolveNightBtn.addEventListener('click', ()=>{
  if(!state.isHost) return alert('只有房主可執行夜晚結果');
  resolveNightBtn.disabled = true;
  google.script.run.withSuccessHandler(res=>{
    resolveNightBtn.disabled = false;
  }).resolveNight(state.roomId, state.playerId);
});
resolveVotesBtn.addEventListener('click', ()=>{
  if(!state.isHost) return alert('只有房主可計算投票');
  resolveVotesBtn.disabled = true;
  google.script.run.withSuccessHandler(res=>{
    resolveVotesBtn.disabled = false;
  }).resolveVotes(state.roomId, state.playerId);
});
leaveBtn.addEventListener('click', ()=>{
  if(!state.roomId || !state.playerId) return;
  google.script.run.withSuccessHandler(()=>{ location.reload(); }).leaveRoom(state.roomId, state.playerId);
});

/* 讀取 avatar（DataURL） */
function _readAvatar(){
  return new Promise((resolve)=>{
    const f = avatarFileEl.files && avatarFileEl.files[0];
    if(!f) return resolve('');
    const r = new FileReader();
    r.onload = ()=> resolve(r.result);
    r.readAsDataURL(f);
  });
}

/* 進入房間 UI */
function _enterRoomUI(){
  document.getElementById('lobby').style.display = 'none';
  roomPanel.style.display = '';
  roomLabel.textContent = state.roomId;
}

/* Polling */
let pollingHandle = null;
function _startPolling(){
  if(pollingHandle) return;
  _pollOnce();
  pollingHandle = setInterval(_pollOnce, POLL_INTERVAL);
}
function _pollOnce(){
  if(!state.roomId) return;
  google.script.run.withSuccessHandler(renderRoom).getRoomState(state.roomId, state.playerId);
}

/* render room state */
function renderRoom(room){
  if(!room || room.error){ gameStateEl.textContent = room && room.error ? room.error : '房間不存在'; return; }
  // players
  playersWrap.innerHTML = '';
  Object.values(room.players || {}).forEach(p=>{
    const div = document.createElement('div'); div.className='player';
    div.innerHTML = `<img class="avatar" src="${p.avatar || 'https://via.placeholder.com/64?text=?'}" />
                     <div><b>${p.name}</b></div>
                     <div class="small">${p.alive ? '' : '已出局'}</div>
                     <div class="small">角色：${p.role ? p.role : (p.id===state.playerId ? '你' : (room.phase==='rolesAssigned' ? '已分配' : '-'))}</div>`;
    playersWrap.appendChild(div);
  });
  // chat
  chatBox.innerHTML = '';
  (room.chat || []).forEach(m=>{
    const el = document.createElement('div');
    if(m.system) el.innerHTML = `<small class="small">${new Date(m.time).toLocaleTimeString()}</small> ${m.text}`;
    else el.innerHTML = `<small class="small">${new Date(m.time).toLocaleTimeString()}</small> <b>${m.name}:</b> ${m.text}`;
    chatBox.appendChild(el);
  });
  chatBox.scrollTop = chatBox.scrollHeight;
  // game state
  gameStateEl.textContent = `Phase: ${room.phase} • Round: ${room.round || 0}`;
  // action area: 根據 phase 與我的 role 顯示可做的操作
  actionArea.innerHTML = '';
  if(room.phase === 'rolesAssigned'){
    actionArea.innerHTML = '<div class="small">等待房主開始夜晚（或房主點執行）</div>';
  } else if(room.phase === 'night'){
    // 顯示我的夜晚操作（若我還活著）
    const me = room.players[state.playerId];
    if(me && me.alive){
      if(me.role === 'werewolf'){
        actionArea.innerHTML = '<div class="small">狼人：選擇要攻擊的目標</div>';
        _renderTargetList(room, 'kill');
      } else if(me.role === 'seer'){
        actionArea.innerHTML = '<div class="small">預言家：選擇一位查看身分（不能看自己）</div>';
        _renderTargetList(room, 'check');
      } else if(me.role === 'doctor'){
        actionArea.innerHTML = '<div class="small">守衛：選擇要保護的玩家（可以保護自己）</div>';
        _renderTargetList(room, 'save');
      } else {
        actionArea.innerHTML = '<div class="small">夜晚：請等待特殊身分行動</div>';
      }
    } else {
      actionArea.innerHTML = '<div class="small">你已陣亡或未加入，等待夜晚結束</div>';
    }
    // 若我是房主，顯示執行夜晚按鈕（已在 header）
  } else if(room.phase === 'day'){
    actionArea.innerHTML = '<div class="small">白天：討論並投票處決嫌疑人</div>';
    _renderVoteList(room);
  } else if(room.phase === 'ended'){
    actionArea.innerHTML = `<div class="small">遊戲結束。勝利：${room.winner || ' - '}</div>`;
  } else {
    actionArea.innerHTML = '<div class="small">等待玩家加入或房主分配身分</div>';
  }

  // 如果房主，顯示管理按鈕（UI 由 header button 顯示）
}

/* render target list for actions */
function _renderTargetList(room, actionType){
  const list = document.createElement('div'); list.className='players';
  Object.values(room.players || {}).forEach(p=>{
    if(!p.alive) return;
    if(actionType==='check' && p.id === state.playerId) return; // seer 不能看自己
    const btn = document.createElement('div'); btn.className='player';
    btn.innerHTML = `<div>${p.name}</div>`;
    btn.onclick = ()=>{
      const confirmMsg = (actionType==='kill' ? `確定要選擇攻擊 ${p.name}？` : (actionType==='check' ? `確定查看 ${p.name} 身分？` : `確定保護 ${p.name}？`));
      if(!confirm(confirmMsg)) return;
      // call submitNightAction
      google.script.run.withSuccessHandler(()=>{ /* no-op */ }).submitNightAction(state.roomId, state.playerId, { type: actionType === 'kill' ? 'kill' : (actionType==='check' ? 'check' : 'save'), targetId: p.id });
      actionArea.innerHTML = '<div class="small">已發出操作，等待夜晚結算</div>';
    };
    list.appendChild(btn);
  });
  actionArea.appendChild(list);
}

/* render voting list (day) */
function _renderVoteList(room){
  const list = document.createElement('div'); list.className='players';
  Object.values(room.players || {}).forEach(p=>{
    if(!p.alive) return;
    const btn = document.createElement('div'); btn.className='player';
    btn.innerHTML = `<div>${p.name}</div>`;
    btn.onclick = ()=>{
      if(!confirm(`確定投票處決 ${p.name} 嗎？`)) return;
      google.script.run.withSuccessHandler(()=>{ /* ok */ }).submitVote(state.roomId, state.playerId, p.id);
    };
    list.appendChild(btn);
  });
  actionArea.appendChild(list);
}
</script>
