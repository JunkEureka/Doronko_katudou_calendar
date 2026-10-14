"use client";
import React, { useState, useEffect, useRef } from "react";

// サークル独自の学年カラー（黄緑、黄色、紫、ピンク）
const gradeColors: { [key: string]: string } = {
  "1年": "border-lime-500 text-lime-600 bg-lime-50 ring-lime-500/10",
  "2年": "border-amber-400 text-amber-600 bg-amber-50 ring-amber-400/10",
  "3年": "border-fuchsia-400 text-fuchsia-600 bg-fuchsia-50 ring-fuchsia-500/10",
  "4年": "border-pink-400 text-pink-600 bg-pink-50 ring-pink-500/10",
};

// 指定された15か所の活動先リスト
const locationsList = [
  "ありんこ", "一ノ宮", "押立", "片町", "上田", 
  "是政", "桜まつり", "新町", "神明", "白糸", 
  "多摩境", "南平", "紅葉", "由木", "四谷"
];

// 初期イベントデータ
const initialEvents: { [key: string]: any } = {
  "2026-11-15": { 
    id: 1, title: "神明あそび相手ボランティア", location: "神明", color: "bg-pink-100 text-pink-700 border-pink-200", limit: 12, 
    startTime: "13:00", endTime: "16:00", items: "上履き、動きやすい服装", description: "子どもたちと一緒に外遊びやレクを行います！", 
    attendees: [{ name: "どろね", grade: "3年" }], lineGroupUrl: "https://line.me/example" 
  },
  "2026-11-18": { 
    id: 2, title: "白糸お芋掘り補助", location: "白糸", color: "bg-emerald-100 text-emerald-700 border-emerald-200", limit: 6, 
    startTime: "10:00", endTime: "12:00", items: "軍手、汚れてもいい服", description: "園児たちのお芋掘りのお手伝いです。", 
    attendees: [], lineGroupUrl: "" 
  },
  "2026-11-21": { 
    id: 3, title: "南平シニアお話し相手", location: "南平", color: "bg-amber-100 text-amber-700 border-amber-200", limit: 8, 
    startTime: "14:00", endTime: "17:00", items: "特になし", description: "お話し相手や簡単なレクリエーションを行います。", 
    attendees: [{ name: "みく", grade: "2年" }], lineGroupUrl: "" 
  },
};

export default function CircleCalendar() {
  const [events, setEvents] = useState(initialEvents);
  const [selectedDate, setSelectedDate] = useState<string | null>("2026-11-15");
  
  // 画面の切り替え状態
  const [currentView, setCurrentView] = useState<"calendar" | "mypage">("calendar");

  // 詳細表示エリアの参照（スマホ用スクロール用）
  const detailSectionRef = useRef<HTMLDivElement>(null);

  // プロフィール情報
  const [myName, setMyName] = useState("");
  const [myGrade, setMyGrade] = useState("1年");
  const [isNameSaved, setIsNameSaved] = useState(false);
  const [myJoinedList, setMyJoinedList] = useState<string[]>([]);

  // 権限機能：館付（リーダー）パスワード
  const [isAdmin, setIsAdmin] = useState(false);
  const [inputPassword, setInputPassword] = useState("");

  // 館付専用：イベント追加フォームの状態
  const [adminDate, setAdminDate] = useState("2026-11-15");
  const [adminTitle, setAdminTitle] = useState("");
  const [adminLocation, setAdminLocation] = useState(locationsList[0]);
  const [adminLimit, setAdminLimit] = useState(10);
  const [adminStartTime, setAdminStartTime] = useState("13:00");
  const [adminEndTime, setAdminEndTime] = useState("16:00");
  const [adminItems, setAdminItems] = useState("");
  const [adminDesc, setAdminDesc] = useState("");
  const [adminLineUrl, setAdminLineUrl] = useState("");

  useEffect(() => {
    const savedName = localStorage.getItem("doronko_my_name");
    const savedGrade = localStorage.getItem("doronko_my_grade");
    const savedList = localStorage.getItem("my_joined_events");
    const savedAdmin = localStorage.getItem("doronko_is_admin");

    if (savedName && savedGrade) {
      setMyName(savedName);
      setMyGrade(savedGrade);
      setIsNameSaved(true);
    }
    if (savedList) setMyJoinedList(JSON.parse(savedList));
    if (savedAdmin === "true") setIsAdmin(true);
  }, []);

  // カレンダーの日付をクリックしたときに選択日を更新＆スマホ用に詳細へ自動スクロール
  const handleDateClick = (dateStr: string) => {
    setSelectedDate(dateStr);
    setAdminDate(dateStr);

    // スマホ表示時、日付をタップしたら自動で詳細部分にスクロール
    setTimeout(() => {
      if (detailSectionRef.current && window.innerWidth < 1024) {
        detailSectionRef.current.scrollIntoView({ behavior: "smooth", block: "start" });
      }
    }, 100);
  };

  const handleSaveProfile = () => {
    if (!myName.trim()) return alert("名前（どろね）を入力してください");
    localStorage.setItem("doronko_my_name", myName);
    localStorage.setItem("doronko_my_grade", myGrade);
    setIsNameSaved(true);
  };

  const handleAuthAdmin = () => {
    if (inputPassword === "doronko15") {
      setIsAdmin(true);
      localStorage.setItem("doronko_is_admin", "true");
      alert("館付（リーダー）権限を認証しました！メニューが解放されます。");
      setInputPassword("");
    } else {
      alert("パスワードが正しくありません。");
    }
  };

  const handleJoin = (date: string) => {
    if (!isNameSaved) return alert("先にプロフィールを確定してください！");
    const event = events[date];
    if (event.attendees.length >= event.limit) return alert("満員です！");
    if (event.attendees.some((a: any) => a.name === myName) || myJoinedList.includes(date)) {
      return alert("すでに登録されています！");
    }

    setEvents({
      ...events,
      [date]: { ...event, attendees: [...event.attendees, { name: myName, grade: myGrade }] },
    });

    const updatedList = [...myJoinedList, date];
    setMyJoinedList(updatedList);
    localStorage.setItem("my_joined_events", JSON.stringify(updatedList));

    if (event.lineGroupUrl) {
      alert(`登録完了！専用LINEグループ：\n${event.lineGroupUrl}`);
    }
  };

  const handleCancel = (date: string) => {
    if (!confirm("本当に参加を取り消しますか？")) return;
    const event = events[date];
    const updatedEvents = {
      ...events,
      [date]: { ...event, attendees: event.attendees.filter((a: any) => a.name !== myName) },
    };
    setEvents(updatedEvents);

    const updatedList = myJoinedList.filter((d) => d !== date);
    setMyJoinedList(updatedList);
    localStorage.setItem("my_joined_events", JSON.stringify(updatedList));
  };

  const handleCopyLineText = (date: string, ev: any) => {
    const text = `🌱【どろんこ活動のお知らせ】🌱
📅 日時: ${date} (${ev.startTime || "未定"}〜${ev.endTime || "未定"})
📍 活動先: ${ev.location}
📌 活動名: ${ev.title}
🎒 持ち物: ${ev.items || "特になし"}
📝 内容: ${ev.description || "なし"}
👥 募集人数: あと ${ev.limit - ev.attendees.length}人（定員 ${ev.limit}人）

参加したい人はカレンダーアプリから登録してね！
${ev.lineGroupUrl ? `💬 LINEグループ: ${ev.lineGroupUrl}` : ""}`;

    navigator.clipboard.writeText(text);
    alert("LINE共有用の文章をコピーしました！LINEにそのまま貼り付けられます。");
  };

  const handleAddEvent = (e: React.FormEvent) => {
    e.preventDefault();
    if (!adminDate || !adminTitle.trim()) return alert("活動日と活動名を入力してください！");

    const newEv = {
      id: Date.now(), 
      title: adminTitle, 
      location: adminLocation,
      color: "bg-sky-100 text-sky-700 border-sky-200", 
      limit: Number(adminLimit),
      startTime: adminStartTime,
      endTime: adminEndTime,
      items: adminItems,
      description: adminDesc,
      attendees: [], 
      lineGroupUrl: adminLineUrl,
    };

    setEvents({
      ...events,
      [adminDate]: newEv
    });

    if (confirm(`${adminDate} に活動を登録しました！LINE用の案内文を今すぐコピーしますか？`)) {
      handleCopyLineText(adminDate, newEv);
    }

    setAdminTitle(""); 
    setAdminItems("");
    setAdminDesc("");
    setAdminLineUrl("");
  };

  const urgentEvents = Object.keys(events)
    .map((date) => ({ date, ...events[date] }))
    .filter((ev) => ev.attendees.length < ev.limit && (ev.attendees.length / ev.limit) < 0.5);

  const daysInMonth = Array.from({ length: 30 }, (_, i) => i + 1);
  const activeEvent = selectedDate ? events[selectedDate] : null;

  return (
    <div className="min-h-screen bg-slate-50 text-slate-800 p-3 sm:p-4 font-sans">
      {/* ナビゲーションヘッダー */}
      <header className="max-w-6xl mx-auto mb-4 sm:mb-6">
        <div className="flex flex-col sm:flex-row justify-between items-center bg-white p-3.5 sm:p-4 rounded-2xl border border-slate-200 shadow-sm gap-3">
          <h1 className="text-xl sm:text-2xl font-black text-amber-800 tracking-tight flex items-center gap-1.5">
            🌱 どろんこ 活動カレンダー
          </h1>
          <div className="flex w-full sm:w-auto bg-slate-100 p-1 rounded-xl border border-slate-200">
            <button
              onClick={() => setCurrentView("calendar")}
              className={`flex-1 sm:flex-none px-3 sm:px-4 py-2 text-xs font-bold rounded-lg transition-all ${
                currentView === "calendar"
                  ? "bg-white text-amber-800 shadow-sm"
                  : "text-slate-500 hover:text-slate-800"
              }`}
            >
              📅 カレンダー
            </button>
            <button
              onClick={() => setCurrentView("mypage")}
              className={`flex-1 sm:flex-none px-3 sm:px-4 py-2 text-xs font-bold rounded-lg transition-all ${
                currentView === "mypage"
                  ? "bg-white text-amber-800 shadow-sm"
                  : "text-slate-500 hover:text-slate-800"
              }`}
            >
              👤 マイページ ＆ 館付
            </button>
          </div>
        </div>
      </header>

      {/* メインコンテンツエリア */}
      <div className="max-w-6xl mx-auto">
        {currentView === "calendar" ? (
          /* 【活動カレンダー画面】 */
          <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
            {/* カレンダー ＆ 急募コーナー */}
            <div className="lg:col-span-2 space-y-4 sm:space-y-6">
              <div className="bg-white rounded-2xl p-3.5 sm:p-6 border border-slate-200 shadow-sm">
                <h2 className="text-lg sm:text-xl font-bold mb-3 sm:mb-4 text-amber-700 text-center font-mono">2026年 11月</h2>
                <div className="grid grid-cols-7 gap-1 sm:gap-2 text-center text-xs font-bold text-slate-400 mb-2">
                  <div className="text-rose-500">日</div><div>月</div><div>火</div><div>水</div><div>木</div><div>金</div><div className="text-sky-500">土</div>
                </div>

                <div className="grid grid-cols-7 gap-1 sm:gap-2">
                  {daysInMonth.map((day) => {
                    const dateStr = `2026-11-${String(day).padStart(2, "0")}`;
                    const hasEvent = events[dateStr];
                    const isSelected = selectedDate === dateStr;

                    return (
                      <div
                        key={day}
                        onClick={() => handleDateClick(dateStr)}
                        className={`h-16 sm:h-20 border rounded-xl p-1 cursor-pointer transition-all flex flex-col justify-between active:scale-95 ${
                          isSelected 
                            ? "border-amber-500 bg-amber-50/50 ring-2 ring-amber-500/20" 
                            : hasEvent 
                              ? "border-slate-300 bg-slate-50 hover:border-amber-400" 
                              : "border-slate-100 bg-white hover:border-slate-300"
                        }`}
                      >
                        <span className="text-[10px] sm:text-xs font-bold font-mono text-slate-400">{day}</span>
                        {hasEvent && (
                          <div className="w-full">
                            <div className={`text-[8px] sm:text-[9px] px-0.5 sm:px-1 py-0.5 rounded truncate font-bold border ${hasEvent.color}`}>
                              {hasEvent.location}
                            </div>
                            <div className="text-[8px] sm:text-[9px] text-right font-bold text-amber-700 font-mono mt-0.5">
                              {hasEvent.attendees.length}/{hasEvent.limit}人
                            </div>
                          </div>
                        )}
                      </div>
                    );
                  })}
                </div>
              </div>

              {/* 大急募セクション */}
              <div className="bg-rose-50 rounded-2xl p-4 sm:p-5 border border-rose-200 shadow-sm">
                <h3 className="text-sm sm:text-md font-black text-rose-700 mb-2.5 flex items-center gap-1">
                  🚨 人手が足りていません！【館付からの大急募】
                </h3>
                {urgentEvents.length > 0 ? (
                  <div className="grid grid-cols-1 sm:grid-cols-2 gap-2.5">
                    {urgentEvents.map((ev) => (
                      <div 
                        key={ev.id}
                        onClick={() => handleDateClick(ev.date)}
                        className="bg-white hover:bg-rose-100/50 border border-rose-300 rounded-xl p-2.5 sm:p-3 cursor-pointer transition-all flex justify-between items-center shadow-xs active:scale-98"
                      >
                        <div>
                          <div className="text-[10px] text-slate-400 font-mono">{ev.date}</div>
                          <div className="text-xs sm:text-sm font-bold text-slate-800 truncate max-w-[140px] sm:max-w-[150px]">{ev.title}</div>
                        </div>
                        <span className="text-[10px] sm:text-xs font-bold px-2 py-1 bg-rose-100 border border-rose-300 rounded-lg text-rose-700">
                          あと {ev.limit - ev.attendees.length}人！
                        </span>
                      </div>
                    ))}
                  </div>
                ) : (
                  <p className="text-xs text-slate-500 font-medium italic">
                    現在、急募中の活動はありません。みんなありがとう！
                  </p>
                )}
              </div>
            </div>

            {/* 右側：選択した日の詳細（スマホタップ時にここへスクロール） */}
            <div ref={detailSectionRef} className="bg-white rounded-2xl p-4 sm:p-5 border border-slate-200 shadow-sm h-fit scroll-mt-4">
              {selectedDate ? (
                activeEvent ? (
                  <div>
                    <div className="border-b border-slate-200 pb-3 mb-3.5">
                      <span className="text-xs text-slate-400 font-mono">{selectedDate}</span>
                      <h2 className="text-base sm:text-lg font-black text-slate-800 mt-0.5">{activeEvent.title}</h2>
                      <div className="flex flex-wrap gap-2 mt-1">
                        <span className="text-xs text-amber-700 font-bold">📍 拠点: {activeEvent.location}</span>
                        {activeEvent.startTime && (
                          <span className="text-xs text-slate-500 font-mono">⏰ {activeEvent.startTime} 〜 {activeEvent.endTime}</span>
                        )}
                      </div>
                    </div>

                    {/* 詳細情報 */}
                    <div className="space-y-2 mb-4 text-xs bg-slate-50 p-3 rounded-xl border border-slate-200">
                      <div><span className="font-bold text-slate-500">🎒 持ち物:</span> {activeEvent.items || "特になし"}</div>
                      <div><span className="font-bold text-slate-500">📝 内容:</span> {activeEvent.description || "記載なし"}</div>
                      {activeEvent.lineGroupUrl && (
                        <div>
                          <a href={activeEvent.lineGroupUrl} target="_blank" rel="noopener noreferrer" className="text-emerald-600 font-bold hover:underline">
                            💬 専用LINEグループはこちら ➔
                          </a>
                        </div>
                      )}
                      <div className="pt-2 border-t border-slate-200">
                        <button
                          onClick={() => handleCopyLineText(selectedDate, activeEvent)}
                          className="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-2 rounded-lg text-xs transition-all flex items-center justify-center gap-1 shadow-xs active:scale-95"
                        >
                          📋 LINE共有用の案内文をコピー
                        </button>
                      </div>
                    </div>

                    {/* メーター */}
                    <div className="mb-4">
                      <div className="flex justify-between items-center text-xs text-slate-500 font-medium mb-1">
                        <span>現在の集まり具合</span>
                        <span className="font-bold text-amber-700">
                          {activeEvent.attendees.length} / {activeEvent.limit} 人
                        </span>
                      </div>
                      <div className="w-full bg-slate-100 rounded-full h-2.5 overflow-hidden border border-slate-200">
                        <div 
                          className="bg-gradient-to-r from-amber-500 to-emerald-500 h-2.5 rounded-full transition-all duration-500" 
                          style={{ width: `${(activeEvent.attendees.length / activeEvent.limit) * 100}%` }}
                        ></div>
                      </div>
                    </div>

                    {/* 参加メンバー */}
                    <div className="mb-5">
                      <h3 className="text-xs font-bold text-slate-400 uppercase tracking-wider mb-2">
                        👥 現在のメンバー（{activeEvent.attendees.length}人）
                      </h3>
                      <div className="grid grid-cols-3 gap-2">
                        {activeEvent.attendees.map((attendee: any, idx: number) => {
                          const isMe = attendee.name === myName;
                          return (
                            <div key={idx} className="relative flex flex-col items-center p-1.5 bg-slate-50 rounded-xl border border-slate-200">
                              <div className={`w-8 h-8 sm:w-9 sm:h-9 rounded-full border-2 flex items-center justify-center font-black text-xs sm:text-sm shadow-xs mb-1 ${gradeColors[attendee.grade] || "border-slate-300"}`}>
                                {attendee.name.charAt(0)}
                              </div>
                              <span className="text-[10px] text-slate-700 font-bold truncate w-full text-center">
                                {attendee.name}
                              </span>
                              <span className="text-[8px] text-slate-400 font-medium">{attendee.grade}</span>
                              
                              {isMe && (
                                <button
                                  onClick={() => handleCancel(selectedDate)}
                                  className="absolute -top-1 -right-1 bg-rose-500 text-white rounded-full w-4 h-4 text-[9px] font-bold flex items-center justify-center shadow-sm hover:bg-rose-600"
                                >
                                  ×
                                </button>
                              )}
                            </div>
                          );
                        })}
                      </div>
                    </div>

                    {/* 登録ボタン */}
                    <div className="bg-slate-50 p-3 rounded-xl border border-slate-200">
                      {!isNameSaved ? (
                        <div className="text-center p-2 text-xs text-slate-500">
                          上部の「マイページ」から名前と学年を登録すると、活動に参加できるようになります！
                        </div>
                      ) : (
                        <div>
                          {myJoinedList.includes(selectedDate) ? (
                            <div className="w-full text-center bg-emerald-50 border border-emerald-300 text-emerald-700 font-bold py-2 rounded-lg text-xs">
                              ✓ 参加登録済み
                            </div>
                          ) : (
                            <button
                              onClick={() => handleJoin(selectedDate)}
                              className="w-full bg-gradient-to-r from-amber-500 to-orange-500 hover:from-amber-600 hover:to-orange-600 text-white font-bold py-2.5 rounded-lg text-xs shadow-md active:scale-95 transition-all"
                            >
                              参加登録を確定する！
                            </button>
                          )}
                        </div>
                      )}
                    </div>
                  </div>
                ) : (
                  <div className="text-center py-6 text-slate-400 text-xs font-medium">
                    {selectedDate} は活動予定がありません。<br />マイページ（館付設定）から新しく登録できます！
                  </div>
                )
              ) : (
                <div className="text-center py-8 text-slate-400 text-xs font-medium">
                  👈 カレンダーの日付をタップすると詳細が表示されます。
                </div>
              )}
            </div>
          </div>
        ) : (
          /* 【マイページ ＆ 館付設定画面】 */
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
            <div className="md:col-span-2 space-y-6">
              {/* プロフィール設定 */}
              <div className="bg-white rounded-2xl p-4 sm:p-6 border border-slate-200 shadow-sm">
                <h3 className="text-md font-bold text-slate-800 mb-3 flex items-center gap-1">
                  👤 プロフィール設定（初回のみ）
                </h3>
                <div className="max-w-md space-y-3">
                  <div>
                    <label className="block text-xs font-bold text-slate-500 mb-1">サークル内の名前（どろね）</label>
                    <input
                      type="text"
                      placeholder="例: どろね"
                      value={myName}
                      onChange={(e) => setMyName(e.target.value)}
                      disabled={isNameSaved}
                      className="w-full bg-slate-50 border border-slate-300 rounded-lg px-3 py-2 text-sm text-slate-800 disabled:opacity-60 focus:outline-none focus:border-amber-500"
                    />
                  </div>
                  <div>
                    <label className="block text-xs font-bold text-slate-500 mb-1">学年</label>
                    <select
                      value={myGrade}
                      onChange={(e) => setMyGrade(e.target.value)}
                      disabled={isNameSaved}
                      className="w-full bg-white border border-slate-300 rounded-lg px-3 py-2 text-sm text-slate-700 disabled:opacity-60"
                    >
                      <option value="1年">1年 (黄緑)</option>
                      <option value="2年">2年 (黄色)</option>
                      <option value="3年">3年 (紫)</option>
                      <option value="4年">4年 (ピンク)</option>
                    </select>
                  </div>
                  {!isNameSaved ? (
                    <button
                      onClick={handleSaveProfile}
                      className="bg-amber-600 hover:bg-amber-700 text-white font-bold px-4 py-2 rounded-lg text-xs shadow-sm active:scale-95"
                    >
                      この情報で名前を固定する（変更不可）
                    </button>
                  ) : (
                    <div className="flex items-center gap-2 text-xs text-emerald-600 font-bold bg-emerald-50 px-3 py-2 rounded-lg border border-emerald-200 w-fit">
                      ✓ 名前が固定されました！
                      <button
                        onClick={() => {
                          if (confirm("名前を変更しますか？")) {
                            localStorage.removeItem("doronko_my_name");
                            setIsNameSaved(false);
                          }
                        }}
                        className="text-amber-700 underline font-medium ml-2"
                      >
                        変更
                      </button>
                    </div>
                  )}
                </div>
              </div>

              {/* 参加予定一覧 */}
              <div className="bg-white rounded-2xl p-4 sm:p-6 border border-slate-200 shadow-sm">
                <h3 className="text-md font-bold text-slate-800 mb-3">
                  📅 あなたの参加予定ボランティア一覧
                </h3>
                {myJoinedList.length > 0 ? (
                  <div className="divide-y divide-slate-100">
                    {myJoinedList.map((date, idx) => (
                      <div key={idx} className="py-3 flex justify-between items-center text-xs sm:text-sm">
                        <div className="flex items-center gap-3 sm:gap-4">
                          <span className="font-mono text-slate-400 font-bold">{date}</span>
                          <span className="text-slate-800 font-black truncate max-w-[150px] sm:max-w-none">
                            {events[date]?.title || "活動ボランティア"}
                          </span>
                        </div>
                        <button
                          onClick={() => handleCancel(date)}
                          className="text-xs text-rose-500 font-bold hover:underline shrink-0"
                        >
                          取り消す
                        </button>
                      </div>
                    ))}
                  </div>
                ) : (
                  <p className="text-sm text-slate-400 italic py-4">
                    まだ参加登録している活動はありません。
                  </p>
                )}
              </div>
            </div>

            {/* 館付認証 ＆ フォーム */}
            <div className="bg-white rounded-2xl p-4 sm:p-6 border border-slate-200 shadow-sm h-fit">
              <h3 className="text-md font-black text-amber-800 border-b border-slate-200 pb-2 mb-3">
                🛠️ 館付（リーダー）専用認証
              </h3>
              
              {!isAdmin ? (
                <div className="space-y-3">
                  <p className="text-xs text-slate-500 leading-relaxed">
                    館付さんのみ、パスワードを入れてアンロックしてください。
                  </p>
                  <input
                    type="password"
                    placeholder="館付パスワードを入力..."
                    value={inputPassword}
                    onChange={(e) => setInputPassword(e.target.value)}
                    className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5 text-xs focus:outline-none focus:border-amber-500"
                  />
                  <button
                    onClick={handleAuthAdmin}
                    className="w-full bg-slate-800 hover:bg-slate-700 text-white font-bold py-2 rounded-lg text-xs"
                  >
                    館付権限をアンロックする
                  </button>
                </div>
              ) : (
                <div>
                  <div className="flex justify-between items-center bg-amber-50 border border-amber-200 p-2 rounded-xl text-xs text-amber-800 font-bold mb-4">
                    <span>👑 館付権限：認証済み</span>
                    <button
                      onClick={() => {
                        setIsAdmin(false);
                        localStorage.removeItem("doronko_is_admin");
                      }}
                      className="underline text-[10px] text-slate-500"
                    >
                      ロック
                    </button>
                  </div>

                  <form onSubmit={handleAddEvent} className="space-y-3 text-xs">
                    <h4 className="font-bold text-slate-700">📌 新しい活動の登録</h4>
                    
                    <div>
                      <label className="block text-[10px] font-bold text-slate-500 mb-1">活動日</label>
                      <input
                        type="date"
                        value={adminDate}
                        onChange={(e) => setAdminDate(e.target.value)}
                        className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5 font-mono"
                      />
                    </div>

                    <div>
                      <label className="block text-[10px] font-bold text-slate-500 mb-1">活動先（15か所）</label>
                      <select
                        value={adminLocation}
                        onChange={(e) => setAdminLocation(e.target.value)}
                        className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5"
                      >
                        {locationsList.map((loc) => (
                          <option key={loc} value={loc}>{loc}</option>
                        ))}
                      </select>
                    </div>

                    <div>
                      <label className="block text-[10px] font-bold text-slate-500 mb-1">活動名</label>
                      <input
                        type="text"
                        placeholder="例：神明あそび相手ボランティア"
                        value={adminTitle}
                        onChange={(e) => setAdminTitle(e.target.value)}
                        className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5"
                      />
                    </div>

                    <div className="grid grid-cols-2 gap-2">
                      <div>
                        <label className="block text-[10px] font-bold text-slate-500 mb-1">集合時間</label>
                        <input
                          type="time"
                          value={adminStartTime}
                          onChange={(e) => setAdminStartTime(e.target.value)}
                          className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2 py-1 font-mono text-xs"
                        />
                      </div>
                      <div>
                        <label className="block text-[10px] font-bold text-slate-500 mb-1">解散時間</label>
                        <input
                          type="time"
                          value={adminEndTime}
                          onChange={(e) => setAdminEndTime(e.target.value)}
                          className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2 py-1 font-mono text-xs"
                        />
                      </div>
                    </div>

                    <div>
                      <label className="block text-[10px] font-bold text-slate-500 mb-1">募集人数 (人)</label>
                      <input
                        type="number"
                        min="1"
                        value={adminLimit}
                        onChange={(e) => setAdminLimit(Number(e.target.value))}
                        className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5 font-mono"
                      />
                    </div>

                    <div>
                      <label className="block text-[10px] font-bold text-slate-500 mb-1">持ち物</label>
                      <input
                        type="text"
                        placeholder="例：上履き、水筒、タオル"
                        value={adminItems}
                        onChange={(e) => setAdminItems(e.target.value)}
                        className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5"
                      />
                    </div>

                    <div>
                      <label className="block text-[10px] font-bold text-slate-500 mb-1">内容</label>
                      <textarea
                        rows={2}
                        placeholder="活動の簡単な説明..."
                        value={adminDesc}
                        onChange={(e) => setAdminDesc(e.target.value)}
                        className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5"
                      ></textarea>
                    </div>

                    <div>
                      <label className="block text-[10px] font-bold text-slate-500 mb-1">LINEグループURL</label>
                      <input
                        type="url"
                        placeholder="https://line.me/..."
                        value={adminLineUrl}
                        onChange={(e) => setAdminLineUrl(e.target.value)}
                        className="w-full bg-slate-50 border border-slate-300 rounded-lg px-2.5 py-1.5"
                      />
                    </div>

                    <button
                      type="submit"
                      className="w-full bg-amber-700 hover:bg-amber-800 text-white font-bold py-2.5 rounded-lg text-xs shadow-sm mt-2 active:scale-95 transition-all"
                    >
                      活動スケジュールを確定・公開
                    </button>
                  </form>
                </div>
              )}
            </div>
          </div>
        )}
      </div>
    </div>
  );
}
