<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>심화 탐구 통합 마스터 시스템</title>
    <!-- 라이브러리 로드: 안정적인 실행을 위해 순서와 crossorigin 설정이 중요합니다 -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;700;900&display=swap');
        
        * { box-sizing: border-box; }
        body { 
            font-family: 'Noto Sans KR', sans-serif; 
            -webkit-print-color-adjust: exact; 
            print-color-adjust: exact;
            background-color: #f8fafc;
            margin: 0;
            padding: 0;
        }
        
        .scrollbar-hide::-webkit-scrollbar { display: none; }
        
        /* 인쇄 최적화 설정 */
        @media print {
            .no-print { display: none !important; }
            .print-only { display: block !important; }
            body { background: white !important; }
            .page { 
                margin: 0 !important; 
                padding: 15mm !important; 
                width: 210mm !important; 
                min-height: 297mm !important; 
                box-shadow: none !important;
                border: none !important;
                page-break-after: always;
            }
            #root { display: block !important; }
        }
        .print-only { display: none; }
        
        /* 애니메이션 */
        .fade-in { animation: fadeIn 0.5s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        /* 로딩 화면 */
        #loading-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #0f172a; color: white; display: flex; flex-direction: column;
            align-items: center; justify-content: center; z-index: 9999;
        }
    </style>
</head>
<body>
    <div id="root">
        <div id="loading-screen">
            <div style="width: 50px; height: 50px; border: 5px solid #6366f1; border-top-color: transparent; border-radius: 50%; animation: spin 1s linear infinite; margin-bottom: 25px;"></div>
            <p style="font-weight: 900; letter-spacing: 3px; font-size: 18px;">SYSTEM LOADING</p>
            <p style="font-size: 12px; color: #94a3b8; margin-top: 10px;">시스템을 준비 중입니다. 잠시만 기다려 주세요.</p>
        </div>
    </div>

    <style>@keyframes spin { to { transform: rotate(360deg); } }</style>

    <script type="text/babel">
        const { useState, useEffect } = React;

        const App = () => {
            // 학생 기본 정보 상태
            const [studentInfo, setStudentInfo] = useState({
                school: "", gradeClass: "", name: "", major: ""
            });

            // 1~10차시 모든 탐구 데이터 상태
            const [sessionData, setSessionData] = useState({
                s1_motive: "", s1_question: "", s1_connection: "",
                s2_book: "", s2_author: "", s2_level: "전공 기초", s2_part: "", s2_goal: "학술 보고서",
                s3_depth: "", s3_trace: "",
                s4_keyword: "", s4_fact: "",
                s5_log: "", s5_error: "", s5_result: "",
                s8_claim: "", s8_growth: "", s8_next: "", s8_summary: ""
            });

            const [activeTab, setActiveTab] = useState(0);
            const [aiLoading, setAiLoading] = useState(false);
            const [aiResult, setAiResult] = useState("");
            const [saveStatus, setSaveStatus] = useState("");

            const apiKey = ""; // Gemini API 키 입력

            // 데이터 로드 및 초기화
            useEffect(() => {
                try {
                    const info = localStorage.getItem('master_student_info_gh');
                    const data = localStorage.getItem('master_session_data_gh');
                    if (info) setStudentInfo(JSON.parse(info));
                    if (data) setSessionData(JSON.parse(data));
                } catch (e) {
                    console.error("데이터 로드 중 오류 발생");
                }
                const loader = document.getElementById('loading-screen');
                if (loader) loader.style.display = 'none';
                refreshIcons();
            }, []);

            // 화면 갱신 시 아이콘 처리
            useEffect(() => { 
                const timer = setTimeout(refreshIcons, 150);
                return () => clearTimeout(timer);
            }, [activeTab, aiResult, aiLoading]);

            const refreshIcons = () => { if (window.lucide) window.lucide.createIcons(); };

            const saveToLocal = () => {
                try {
                    localStorage.setItem('master_student_info_gh', JSON.stringify(studentInfo));
                    localStorage.setItem('master_session_data_gh', JSON.stringify(sessionData));
                    setSaveStatus("✅ 브라우저에 저장되었습니다.");
                    setTimeout(() => setSaveStatus(""), 2500);
                } catch (e) {
                    setSaveStatus("❌ 저장에 실패했습니다.");
                }
            };

            const callGemini = async (prompt, systemInstruction) => {
                if (!apiKey) {
                    setAiResult("⚠️ AI 기능을 사용하려면 코드 상단의 apiKey 변수에 구글 제미나이 API 키를 입력해야 합니다.");
                    return;
                }
                setAiLoading(true);
                setAiResult("");
                const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
                
                try {
                    const response = await fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({
                            contents: [{ parts: [{ text: prompt }] }],
                            systemInstruction: { parts: [{ text: systemInstruction }] }
                        })
                    });
                    const result = await response.json();
                    if (result.candidates && result.candidates[0]) {
                        setAiResult(result.candidates[0].content.parts[0].text);
                    } else {
                        throw new Error("Invalid response");
                    }
                } catch (err) {
                    setAiResult("⚠️ AI 연결 오류: 잠시 후 다시 시도하거나 API 키를 확인하세요.");
                } finally {
                    setAiLoading(false);
                }
            };

            const handleAiConsult = () => {
                let system = "너는 고등학생의 심화 탐구 활동을 전문적으로 지도하는 입시 학술 멘토야. 주장-근거-설명 공식을 강조하고, 학생부 세특에 적합한 격조 있는 문장을 추천해줘.";
                let prompt = "";
                if (activeTab === 1) prompt = `전공: ${studentInfo.major}, 학생 질문: ${sessionData.s1_question}. 이 질문을 학문적 깊이가 드러나는 정교한 질문 3가지로 다듬어줘.`;
                else if (activeTab === 8) prompt = `탐구 결과: ${sessionData.s8_claim}. 이 활동을 [동기-과정-성장] 구조로 생기부 세특용 300자 문구로 작성해줘.`;
                else prompt = "현재 탐구 단계에서 학생이 자료의 수준을 전공자 수준으로 높이기 위한 실전 팁을 하나 알려줘.";
                callGemini(prompt, system);
            };

            const handleInput = (e) => {
                const { name, value } = e.target;
                setSessionData(prev => ({ ...prev, [name]: value }));
            };

            const handleStudent = (e) => {
                const { name, value } = e.target;
                setStudentInfo(prev => ({ ...prev, [name]: value }));
            };

            const tabs = [
                { id: 0, title: "기본 정보", icon: "user", color: "bg-slate-900" },
                { id: 11, title: "자료검색법", icon: "search", color: "bg-amber-600" },
                { id: 1, title: "1차시: 질문", icon: "clipboard-list" },
                { id: 2, title: "2차시: 계획", icon: "calendar" },
                { id: 3, title: "3차시: 도서분석", icon: "book-open" },
                { id: 4, title: "4차시: 논문분석", icon: "file-text" },
                { id: 13, title: "탐구 방법론", icon: "bar-chart-3", color: "bg-indigo-600" },
                { id: 5, title: "5~7차시: 수행", icon: "wand-2" },
                { id: 12, title: "AI 가이드", icon: "shield-check", color: "bg-rose-600" },
                { id: 8, title: "8~10차시: 성찰", icon: "graduation-cap" }
            ];

            return (
                <div className="flex flex-col min-h-screen relative">
                    {/* 브라우저 화면용 UI (인쇄 시 숨김) */}
                    <div className="no-print flex-grow flex flex-col">
                        <header className="bg-slate-900 text-white p-5 shadow-2xl flex justify-between items-center sticky top-0 z-50">
                            <div className="flex items-center gap-4">
                                <div className="bg-indigo-600 p-2.5 rounded-2xl">
                                    <i data-lucide="sparkles" className="text-white w-6 h-6 animate-pulse"></i>
                                </div>
                                <div>
                                    <h1 className="text-xl font-black tracking-tighter leading-none">심화 탐구 통합 마스터</h1>
                                    <p className="text-slate-400 text-[10px] font-bold uppercase tracking-widest mt-1">Research Excellence Portal v3.5</p>
                                </div>
                            </div>
                            <div className="flex gap-2">
                                <button onClick={saveToLocal} className="bg-slate-800 hover:bg-slate-700 text-slate-300 px-5 py-2.5 rounded-xl text-xs font-bold flex items-center gap-2 transition-all">
                                    <i data-lucide="save" className="w-4 h-4"></i> 임시 저장
                                </button>
                                <button onClick={() => window.print()} className="bg-indigo-600 hover:bg-indigo-500 text-white px-6 py-2.5 rounded-xl text-xs font-black flex items-center gap-2 shadow-xl transition-all active:scale-95">
                                    <i data-lucide="printer" className="w-4 h-4"></i> 워크북 출력
                                </button>
                            </div>
                        </header>

                        <nav className="bg-white border-b border-slate-200 flex gap-1 overflow-x-auto p-2.5 scrollbar-hide sticky top-[76px] z-40">
                            {tabs.map(tab => (
                                <button
                                    key={tab.id}
                                    onClick={() => { setActiveTab(tab.id); setAiResult(""); }}
                                    className={`flex-shrink-0 px-4 py-3 rounded-2xl border font-black text-[11px] flex items-center gap-2 transition-all ${
                                        activeTab === tab.id 
                                        ? `${tab.color || 'bg-indigo-600'} text-white shadow-xl scale-105 ring-2 ring-indigo-100 ring-offset-2` 
                                        : 'bg-white text-slate-500 border-slate-100 hover:bg-slate-50'
                                    }`}
                                >
                                    <i data-lucide={tab.icon} className="w-3.5 h-3.5"></i> {tab.title}
                                </button>
                            ))}
                        </nav>

                        <main className="flex-grow p-4 md:p-10 bg-slate-50">
                            <div className="max-w-5xl mx-auto pb-24">
                                {saveStatus && <div className="fixed bottom-10 left-1/2 -translate-x-1/2 bg-slate-900 text-white px-10 py-3 rounded-full text-xs font-black shadow-2xl z-50 animate-in slide-in-from-bottom-5">{saveStatus}</div>}

                                {/* AI Result Section */}
                                {(aiLoading || aiResult) && (
                                    <div className="mb-10 bg-white border border-indigo-100 rounded-[40px] p-8 shadow-2xl border-l-[16px] border-l-indigo-600 fade-in">
                                        <div className="flex items-center justify-between mb-4 border-b border-slate-50 pb-4">
                                            <div className="flex items-center gap-3 text-indigo-600 font-black text-xl italic"><i data-lucide="sparkles"></i> AI 튜터의 탐구 피드백</div>
                                            <button onClick={() => setAiResult("")} className="text-slate-300 hover:text-slate-600"><i data-lucide="x" className="w-5 h-5"></i></button>
                                        </div>
                                        {aiLoading ? (
                                            <div className="flex flex-col items-center py-6">
                                                <div className="w-10 h-10 border-4 border-indigo-600 border-t-transparent rounded-full animate-spin mb-4"></div>
                                                <p className="text-sm font-black text-slate-400 animate-pulse uppercase tracking-widest italic">Generating expert advice...</p>
                                            </div>
                                        ) : (
                                            <div className="text-base font-medium leading-relaxed whitespace-pre-wrap text-slate-700 bg-indigo-50/30 p-8 rounded-3xl border border-indigo-50 shadow-inner">{aiResult}</div>
                                        )}
                                    </div>
                                )}

                                {/* Main Card Section */}
                                <div className="bg-white rounded-[50px] border border-slate-200 shadow-2xl p-8 md:p-14 min-h-[700px] flex flex-col fade-in">
                                    
                                    {activeTab === 0 && (
                                        <div className="space-y-12">
                                            <div className="border-b-2 border-slate-50 pb-10">
                                                <h2 className="text-5xl font-black text-slate-900 tracking-tighter italic">정보 입력</h2>
                                                <p className="text-slate-400 font-bold mt-4 text-xs uppercase tracking-[0.4em] underline underline-offset-8 decoration-indigo-200">Personal Identification Profile</p>
                                            </div>
                                            <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
                                                {[
                                                    { id: 'school', label: '학교명', placeholder: 'OO고등학교' },
                                                    { id: 'gradeClass', label: '학번', placeholder: '20101' },
                                                    { id: 'name', label: '성명', placeholder: '홍길동' },
                                                    { id: 'major', label: '희망전공', placeholder: '생명공학, 경영학 등' }
                                                ].map(item => (
                                                    <div key={item.id} className="space-y-4">
                                                        <label className="text-[11px] font-black text-slate-400 uppercase tracking-[0.25em] ml-3">{item.label}</label>
                                                        <input name={item.id} value={studentInfo[item.id]} onChange={handleStudent} className="w-full p-6 rounded-3xl bg-slate-50 border-2 border-transparent focus:border-indigo-600 focus:bg-white focus:shadow-xl outline-none transition-all font-black text-xl" placeholder={item.placeholder} />
                                                    </div>
                                                ))}
                                            </div>
                                            <div className="bg-indigo-50 p-8 rounded-[40px] border border-indigo-100 mt-10">
                                                <p className="text-sm font-bold text-indigo-700 leading-relaxed italic">※ 본 시스템에 입력하신 모든 데이터는 워크북 인쇄 시 자동으로 배치됩니다. 차시별로 정성껏 채워주세요.</p>
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 11 && (
                                        <div className="space-y-10">
                                            <h2 className="text-4xl font-black text-amber-600 flex items-center gap-4 tracking-tighter italic underline decoration-amber-200 underline-offset-4 decoration-8">자료 검색 비기</h2>
                                            <div className="grid gap-8 pt-6">
                                                <div className="bg-amber-50 p-10 rounded-[40px] border border-amber-200 hover:shadow-2xl transition-all">
                                                    <h3 className="font-black text-amber-900 text-2xl mb-4 flex items-center gap-3">1. DBpia 논문 검색 전략</h3>
                                                    <p className="text-base leading-relaxed text-amber-800 font-bold italic opacity-80 underline underline-offset-4 decoration-amber-200">진로 카테고리 → 저널목록 → 최근 1년/인기순 필터 → 상위 논문 10개 핵심 키워드 파악.</p>
                                                    <div className="mt-8 bg-white/60 p-4 rounded-2xl text-xs font-black text-amber-600 inline-block border border-amber-200">💡 팁: 현재 학계가 주목하는 가장 '최신 이슈'를 내 질문에 섞으세요.</div>
                                                </div>
                                                <div className="bg-indigo-50 p-10 rounded-[40px] border border-indigo-200 hover:shadow-2xl transition-all">
                                                    <h3 className="font-black text-indigo-900 text-2xl mb-4 flex items-center gap-3">2. 지식의 계보 (독서 확장)</h3>
                                                    <p className="text-base leading-relaxed text-indigo-800 font-bold italic opacity-80 underline underline-offset-4 decoration-indigo-200">교과서(기초) → 전공 기초서(개론) → 특정 세부 주제별 심화 학술서.</p>
                                                    <div className="mt-8 bg-white/60 p-4 rounded-2xl text-xs font-black text-indigo-600 inline-block border border-indigo-200">💡 팁: 참고문헌 리스트를 따라가며 자료를 수집하는 것이 진짜 공부입니다.</div>
                                                </div>
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 1 && (
                                        <div className="space-y-10 flex-grow">
                                            <div className="flex justify-between items-center border-b pb-8">
                                                <h2 className="text-4xl font-black tracking-tighter text-slate-900 italic underline decoration-indigo-500 decoration-8 underline-offset-4">01. 탐구 질문 생성</h2>
                                                <button onClick={handleAiConsult} className="bg-slate-900 text-white px-10 py-4 rounded-[32px] text-sm font-black flex items-center gap-3 hover:bg-indigo-600 transition-all shadow-2xl active:scale-95"><i data-lucide="sparkles" className="w-5 h-5 text-amber-400"></i> AI 질문 정교화</button>
                                            </div>
                                            <div className="space-y-10">
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-slate-400 uppercase tracking-widest ml-2">탐구 동기 및 계기</label>
                                                    <textarea name="s1_motive" value={sessionData.s1_motive} onChange={handleInput} className="w-full h-56 p-10 rounded-[50px] bg-slate-50 border-none outline-none font-bold text-base focus:ring-8 focus:ring-indigo-50 transition-all leading-relaxed shadow-inner" placeholder="당신의 궁금증이 시작된 구체적 순간을 기록하세요." />
                                                </div>
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-indigo-400 uppercase tracking-widest ml-2">핵심 탐구 질문 (RESEARCH QUESTION)</label>
                                                    <input name="s1_question" value={sessionData.s1_question} onChange={handleInput} className="w-full p-10 rounded-[32px] bg-indigo-50/50 border-4 border-indigo-100 focus:border-indigo-600 focus:bg-white outline-none font-black text-2xl text-indigo-900 transition-all" placeholder="예: ~가 ~에 미치는 정량적 영향 분석" />
                                                </div>
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 2 && (
                                        <div className="space-y-10">
                                            <h2 className="text-4xl font-black text-slate-900 tracking-tighter italic">02. 도서 선정 및 계획</h2>
                                            <div className="grid grid-cols-1 md:grid-cols-2 gap-10 pt-6">
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-slate-400 uppercase ml-2">심화 도서명</label>
                                                    <input name="s2_book" value={sessionData.s2_book} onChange={handleInput} className="w-full p-6 rounded-3xl bg-slate-50 border-none outline-none font-black text-xl" placeholder="책 제목" />
                                                </div>
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-slate-400 uppercase ml-2">도서 수준</label>
                                                    <select name="s2_level" value={sessionData.s2_level} onChange={handleInput} className="w-full p-6 rounded-3xl bg-slate-50 border-none outline-none font-black text-xl appearance-none">
                                                        <option>기초 교양</option>
                                                        <option>전공 기초(개론)</option>
                                                        <option>전문 학술서</option>
                                                    </select>
                                                </div>
                                            </div>
                                            <div className="space-y-4">
                                                <label className="text-[11px] font-black text-slate-400 uppercase ml-2">발췌독 지점 (질문에 답을 줄 핵심 챕터)</label>
                                                <input name="s2_part" value={sessionData.s2_part} onChange={handleInput} className="w-full p-6 rounded-3xl bg-slate-50 border-none outline-none font-black text-xl" placeholder="예: 제3장 비선형적 상관관계 분석 이론" />
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 3 && (
                                        <div className="space-y-10">
                                            <h2 className="text-4xl font-black text-slate-900 tracking-tighter italic">03. 도서 심화 분석</h2>
                                            <div className="space-y-4">
                                                <label className="text-[11px] font-black text-slate-400 uppercase ml-2">지식의 확장 (교과서 이상의 원리)</label>
                                                <textarea name="s3_depth" value={sessionData.s3_depth} onChange={handleInput} className="w-full h-80 p-12 rounded-[60px] bg-slate-50 border-none outline-none font-bold text-lg leading-relaxed shadow-inner" placeholder="책에서 찾은 전문적 지식을 본인만의 언어로 재정의해보세요." />
                                            </div>
                                            <div className="space-y-4">
                                                <label className="text-[11px] font-black text-slate-400 uppercase ml-2">자료 추적 (참고문헌 리스트)</label>
                                                <input name="s3_trace" value={sessionData.s3_trace} onChange={handleInput} className="w-full p-6 rounded-3xl bg-slate-50 border-none outline-none font-black text-xl" placeholder="추가로 확인한 논문이나 도서 제목" />
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 4 && (
                                        <div className="space-y-10">
                                            <h2 className="text-4xl font-black text-slate-900 tracking-tighter italic">04. 논문 데이터 추출</h2>
                                            <div className="space-y-6">
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-slate-400 uppercase ml-2">학계의 최신 트렌드 분석</label>
                                                    <textarea name="s4_keyword" value={sessionData.s4_keyword} onChange={handleInput} className="w-full h-48 p-10 rounded-[40px] bg-slate-50 border-none outline-none font-bold text-base leading-relaxed shadow-inner" placeholder="DBpia 상위 논문들이 공통적으로 다루는 핵심 지점은?" />
                                                </div>
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-indigo-400 uppercase ml-2 font-black italic underline underline-offset-4 decoration-4">핵심 숫자와 팩트 (CORE EVIDENCE)</label>
                                                    <textarea name="s4_fact" value={sessionData.s4_fact} onChange={handleInput} className="w-full h-56 p-12 rounded-[60px] bg-indigo-50/40 border-4 border-dashed border-indigo-100 outline-none font-black text-xl text-indigo-900 leading-relaxed shadow-inner" placeholder="논문에서 찾아낸 구체적인 수치 데이터" />
                                                </div>
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 5 && (
                                        <div className="space-y-10 flex-grow">
                                            <h2 className="text-4xl font-black text-slate-900 tracking-tighter italic">05-07. 수행 및 노력 기록</h2>
                                            <div className="space-y-10 flex-grow">
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-slate-400 uppercase ml-2">진짜 노력 일지 (ACTION LOG)</label>
                                                    <textarea name="s5_log" value={sessionData.s5_log} onChange={handleInput} className="w-full h-96 p-12 rounded-[60px] bg-slate-50 border-none outline-none font-bold text-base leading-relaxed shadow-inner" placeholder="방문 사이트명, 검색어 변경, 데이터 가공 과정 등을 날짜별로 상세히 기록하세요." />
                                                </div>
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-rose-400 uppercase ml-2">시행착오 기록 (ERROR LOG)</label>
                                                    <textarea name="s5_error" value={sessionData.s5_error} onChange={handleInput} className="w-full h-40 p-8 rounded-[40px] bg-rose-50/40 border-none outline-none font-bold text-base leading-relaxed text-rose-900 italic shadow-inner" placeholder="분석 과정에서 막혔던 경험을 솔직하게 남기세요. 실패가 진정성입니다." />
                                                </div>
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 8 && (
                                        <div className="space-y-10 flex-grow">
                                            <div className="flex justify-between items-center border-b pb-8">
                                                <h2 className="text-4xl font-black text-indigo-600 tracking-tighter italic">08-10. 성찰 및 최종 보고</h2>
                                                <button onClick={handleAiConsult} className="bg-indigo-600 text-white px-12 py-5 rounded-[32px] text-sm font-black flex items-center gap-3 hover:bg-indigo-700 transition-all shadow-2xl active:scale-95 shadow-indigo-100"><i data-lucide="sparkles" className="w-6 h-6 text-white"></i> AI 세특 초안 완성</button>
                                            </div>
                                            <div className="space-y-12 flex-grow">
                                                <div className="space-y-4">
                                                    <label className="text-[11px] font-black text-slate-400 uppercase ml-2">최종 결론 (데이터 기반의 나의 주장)</label>
                                                    <textarea name="s8_claim" value={sessionData.s8_claim} onChange={handleInput} className="w-full h-48 p-12 rounded-[60px] bg-slate-50 border-none outline-none font-black text-xl leading-relaxed shadow-inner" placeholder="예: 탐구 결과 ~라는 사실을 수치로 확인하였습니다." />
                                                </div>
                                                <div className="p-16 bg-slate-900 text-white rounded-[100px] shadow-[0_60px_120px_rgba(0,0,0,0.3)] border-b-[20px] border-indigo-600 flex-grow relative overflow-hidden group">
                                                    <div className="absolute top-0 right-0 p-16 opacity-5 transition-opacity group-hover:opacity-10"><i data-lucide="graduation-cap" className="w-80 h-80 text-indigo-400"></i></div>
                                                    <label className="block text-[12px] font-black text-indigo-400 uppercase tracking-[0.6em] mb-12 text-center">🎓 FINAL SCHOLARLY SUMMARY</label>
                                                    <textarea name="s8_summary" value={sessionData.s8_summary} onChange={handleInput} className="w-full h-80 bg-transparent border-none outline-none text-2xl font-black leading-relaxed resize-none scrollbar-hide relative z-10 text-center placeholder:text-slate-800" placeholder="[동기-과정-성장] 구조로 탐구를 화려하게 장식하세요." />
                                                </div>
                                            </div>
                                        </div>
                                    )}

                                    {/* Guides (13, 12) */}
                                    {activeTab === 13 && (
                                        <div className="space-y-12 py-10">
                                            <h2 className="text-4xl font-black text-indigo-600 tracking-tighter italic">📊 탐구 방법론 마스터 가이드</h2>
                                            <div className="grid grid-cols-1 md:grid-cols-2 gap-10">
                                                <div className="bg-indigo-50 p-14 rounded-[60px] border border-indigo-100 shadow-xl group hover:bg-indigo-100 transition-all">
                                                    <h3 className="font-black text-indigo-900 text-3xl mb-8 flex items-center gap-4 italic underline decoration-indigo-200 underline-offset-8">① 비교 분석형</h3>
                                                    <p className="text-lg text-indigo-800 leading-relaxed font-bold opacity-80">A와 B를 동일한 기준(잣대)으로 대조하여 숫자로 그 차이를 밝히는 가장 강력한 학술적 모델입니다.</p>
                                                </div>
                                                <div className="bg-indigo-50 p-14 rounded-[60px] border border-indigo-100 shadow-xl group hover:bg-indigo-100 transition-all">
                                                    <h3 className="font-black text-indigo-900 text-3xl mb-8 flex items-center gap-4 italic underline decoration-indigo-200 underline-offset-8">② 데이터 재해석형</h3>
                                                    <p className="text-lg text-indigo-800 leading-relaxed font-bold opacity-80">기존의 통계 공공 데이터에 나만의 새로운 가설과 변수를 적용해 가치를 발견하는 고난도 분석 모델입니다.</p>
                                                </div>
                                            </div>
                                            <div className="bg-slate-900 p-14 rounded-[80px] text-white shadow-2xl border-l-[20px] border-indigo-500">
                                                <h3 className="font-black mb-8 text-indigo-300 text-3xl italic tracking-tighter">🛡️ 주장-근거-설명(주근설) 문장 공식</h3>
                                                <p className="text-2xl font-black tracking-tight italic leading-relaxed">"[자료출처]에 의하면 [A]는 [B]보다 약 [N]% 더 높은 수치를 보이며, 이는 ~라는 학술적 원인 때문으로 추론됨."</p>
                                            </div>
                                        </div>
                                    )}

                                    {activeTab === 12 && (
                                        <div className="space-y-10 fade-in text-center py-20">
                                            <div className="bg-rose-50 p-20 rounded-[120px] border border-rose-100 max-w-4xl mx-auto shadow-2xl border-b-[20px] border-rose-200">
                                                <i data-lucide="alert-triangle" className="w-32 h-32 text-rose-500 mx-auto mb-12"></i>
                                                <h2 className="text-5xl font-black text-rose-900 tracking-tighter mb-10 italic">AI는 발품을 팔지 못합니다.</h2>
                                                <p className="text-2xl text-rose-800 leading-relaxed font-black mb-16 italic underline decoration-rose-200 underline-offset-[16px] decoration-8">사정관은 매끄러운 AI의 문장보다<br/>여러분의 투박한 '시행착오의 기록'에 만점을 줍니다.</p>
                                                <div className="text-left bg-white/60 p-14 rounded-[60px] space-y-8 font-bold text-lg text-rose-900 border border-rose-100 shadow-inner">
                                                    <p className="flex gap-6 items-center border-b border-rose-100 pb-6"><i data-lucide="check-circle" className="w-8 h-8 text-rose-600"></i> AI에게 탐구의 결론을 통째로 대신 써달라고 하지 마세요.</p>
                                                    <p className="flex gap-6 items-center border-b border-rose-100 pb-6"><i data-lucide="check-circle" className="w-8 h-8 text-rose-600"></i> 내 생각의 허점을 찾아달라고 하여 논리를 정교하게 다듬으세요.</p>
                                                    <p className="flex gap-6 items-center"><i data-lucide="check-circle" className="w-8 h-8 text-rose-600"></i> 데이터 검색 실패나 오류 수정 과정을 당당하게 기록하세요. 그것이 진짜입니다.</p>
                                                </div>
                                            </div>
                                        </div>
                                    )}

                                </div>
                            </div>
                        </main>
                        
                        <footer className="bg-slate-900 text-slate-600 p-8 text-center text-[11px] font-black tracking-[0.8em] uppercase border-t border-slate-800 italic">
                            Academic Mastery Project &middot; 2024 Final Scholarly Edition
                        </footer>
                    </div>

                    {/* 인쇄 전용 레이아웃: 화면에서는 숨겨지고 출력 시에만 나타남 */}
                    <div className="print-only bg-white text-slate-900 p-0 m-0 w-full">
                        {/* 1. 표지 (Page 1) */}
                        <section className="page h-[297mm] flex flex-col items-center justify-center text-center p-24 border-b-[24px] border-slate-900 page-break">
                            <div className="border-[8px] border-slate-900 px-16 py-4 rounded-full font-black text-xl mb-20 uppercase tracking-[1em] italic">Academic Master System</div>
                            <h1 className="text-[100px] font-black leading-none mb-10 tracking-tighter">심화 탐구 통합 리포트<br/>(최종 완결판)</h1>
                            <p className="text-4xl text-slate-400 mt-14 font-black tracking-tight italic opacity-50 underline decoration-indigo-200 decoration-8 underline-offset-[20px]">전문 도서 · 학술 논문 연계 10차시 탐구 프로세스</p>
                            
                            <div className="w-[750px] border-t-[16px] border-slate-900 mt-60 pt-24 space-y-16">
                                <div className="flex justify-between border-b-4 border-slate-100 py-10 font-black text-5xl italic tracking-tighter"><span>학 교 명</span><span className="text-indigo-600 underline underline-offset-[24px] decoration-indigo-100">{studentInfo.school || "________"}</span></div>
                                <div className="flex justify-between border-b-4 border-slate-100 py-10 font-black text-5xl italic tracking-tighter"><span>학년/반/번호</span><span className="text-indigo-600 underline underline-offset-[24px] decoration-indigo-100">{studentInfo.gradeClass || "________"}</span></div>
                                <div className="flex justify-between border-b-4 border-slate-100 py-10 font-black text-5xl italic tracking-tighter"><span>성 명</span><span className="text-indigo-600 underline underline-offset-[24px] decoration-indigo-100">{studentInfo.name || "________"}</span></div>
                                <div className="flex justify-between border-b-4 border-slate-100 py-10 font-black text-5xl italic tracking-tighter"><span>희망 전공</span><span className="text-indigo-600 underline underline-offset-[24px] decoration-indigo-100">{studentInfo.major || "________"}</span></div>
                            </div>
                        </section>

                        {/* 2. 기획 리포트 (Page 2) */}
                        <section className="page p-24 min-h-[297mm] page-break">
                            <div className="flex justify-between items-end border-b-[12px] border-slate-900 pb-12 mb-24">
                                <h2 className="text-7xl font-black tracking-tighter italic">01. 탐구 기획 리포트</h2>
                                <span className="text-slate-300 font-black text-4xl italic tracking-[0.3em]">PAGE 02</span>
                            </div>
                            <div className="space-y-32">
                                <div>
                                    <h3 className="bg-slate-900 text-white px-12 py-5 rounded-2xl inline-block font-black text-3xl mb-12 uppercase italic tracking-widest shadow-2xl">1차시: 탐구 지적 동기 (Motivation)</h3>
                                    <div className="border-[10px] border-slate-50 p-20 rounded-[100px] bg-slate-50/40 min-h-[350px] text-3xl font-bold leading-relaxed whitespace-pre-wrap">{sessionData.s1_motive || "기록된 내용이 없습니다."}</div>
                                    <div className="mt-16 border-[10px] border-indigo-100 p-16 rounded-[100px] bg-indigo-50/30 text-5xl font-black text-indigo-900 italic tracking-tighter shadow-inner flex items-center gap-12">
                                        <span className="bg-indigo-600 text-white w-24 h-24 rounded-full flex items-center justify-center not-italic text-4xl shadow-xl">Q</span>
                                        {sessionData.s1_question || "탐구 질문 미설정"}
                                    </div>
                                </div>
                                <div>
                                    <h3 className="bg-slate-900 text-white px-12 py-5 rounded-2xl inline-block font-black text-3xl mb-12 uppercase italic tracking-widest shadow-2xl">2차시: 연구 대상 도서 분석</h3>
                                    <div className="grid grid-cols-3 gap-16 text-center">
                                        <div className="border-[8px] p-16 rounded-[80px] shadow-sm"><span className="text-sm text-slate-400 font-black block mb-8 uppercase tracking-[0.6em] italic">도서명</span><span className="text-3xl font-black leading-tight italic">{sessionData.s2_book || "-"}</span></div>
                                        <div className="border-[8px] p-16 rounded-[80px] shadow-sm"><span className="text-sm text-slate-400 font-black block mb-8 uppercase tracking-[0.6em] italic">난이도</span><span className="text-3xl font-black leading-tight italic">{sessionData.s2_level || "-"}</span></div>
                                        <div className="border-[8px] p-16 rounded-[80px] shadow-sm"><span className="text-sm text-slate-400 font-black block mb-8 uppercase tracking-[0.6em] italic">발췌독</span><span className="text-3xl font-black leading-tight italic">{sessionData.s2_part || "-"}</span></div>
                                    </div>
                                </div>
                            </div>
                        </section>

                        {/* 3. 수행 및 결과 리포트 (Page 3) */}
                        <section className="page p-24 min-h-[297mm]">
                            <div className="flex justify-between items-end border-b-[12px] border-slate-900 pb-12 mb-24">
                                <h2 className="text-7xl font-black tracking-tighter italic">02. 수행 로그 및 최종 리포트</h2>
                                <span className="text-slate-300 font-black text-4xl italic tracking-[0.3em]">PAGE 03</span>
                            </div>
                            <div className="space-y-24">
                                <div>
                                    <h3 className="bg-slate-900 text-white px-12 py-5 rounded-2xl inline-block font-black text-3xl mb-12 uppercase italic shadow-2xl">5~7차시: 진짜 노력 일지 (Log)</h3>
                                    <div className="border-[10px] border-slate-50 p-20 rounded-[100px] min-h-[650px] text-3xl font-bold leading-[1.6] whitespace-pre-wrap shadow-inner">{sessionData.s5_log || "활동 기록 미입력"}</div>
                                </div>
                                <div className="pt-20 relative">
                                    <h3 className="bg-indigo-900 text-white px-20 py-8 rounded-full inline-block font-black text-3xl mb-16 tracking-[0.6em] shadow-[0_30px_60px_rgba(79,70,229,0.3)] relative z-10 italic">🎓 최종 성찰 및 학생부 요약</h3>
                                    <div className="border-[24px] border-slate-900 p-24 rounded-[150px] bg-white min-h-[750px] text-5xl font-black text-slate-900 leading-[1.5] tracking-tighter shadow-2xl relative">
                                        <div className="absolute bottom-0 right-0 p-32 opacity-[0.03] rotate-12"><i data-lucide="sparkles" className="w-96 h-96 text-indigo-900"></i></div>
                                        {sessionData.s8_summary || "최종 요약본 미작성"}
                                    </div>
                                </div>
                            </div>
                            <p className="text-center mt-56 text-lg font-black text-slate-300 uppercase tracking-[2em] opacity-40 italic">End of Master Scholarly Report</p>
                        </section>
                    </div>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
