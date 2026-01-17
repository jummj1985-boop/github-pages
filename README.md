# github-pages
import React, { useState, useEffect, useCallback, useMemo } from 'react';
import { HashRouter as Router, Routes, Route, useNavigate } from 'react-router-dom';
import { 
  Loader2, 
  ArrowRight, 
  XCircle, 
  ChevronLeft, 
  PieChart, 
  Gem, 
  Ghost, 
  TrendingUp, 
  Users, 
  Download,
  Orbit,
  Check,
  AlertCircle
} from 'lucide-react';

// --- Constants & Data ---

const TYPE_DETAILS = {
  1: {
    name: 'The Reformer',
    nickname: '개혁가',
    color: 'bg-red-500',
    shortDescription: '원칙적이고 이상적인 유형',
    description: '공정함과 올바름을 추구하며, 완벽을 기하려는 욕구가 강합니다.'
  },
  2: {
    name: 'The Helper',
    nickname: '조력가',
    color: 'bg-orange-500',
    shortDescription: '배려하고 인간관계 지향적인 유형',
    description: '타인에게 도움을 주며 사랑받기를 원합니다.'
  },
  3: {
    name: 'The Achiever',
    nickname: '성취가',
    color: 'bg-yellow-500',
    shortDescription: '성공 지향적이고 실용적인 유형',
    description: '성취와 유능함을 중요시하며, 목표 달성에 에너지를 쏟습니다.'
  },
  4: {
    name: 'The Individualist',
    nickname: '예술가',
    color: 'bg-emerald-500',
    shortDescription: '낭만적이고 내향적인 유형',
    description: '독특한 자아를 찾고 싶어하며, 감정의 깊이를 중시합니다.'
  },
  5: {
    name: 'The Investigator',
    nickname: '탐구가',
    color: 'bg-sky-500',
    shortDescription: '강렬하고 지적인 유형',
    description: '지식과 이해를 추구하며, 혼자만의 시간을 통해 에너지를 충전합니다.'
  },
  6: {
    name: 'The Loyalist',
    nickname: '충실가',
    color: 'bg-indigo-500',
    shortDescription: '안전을 추구하고 헌신적인 유형',
    description: '책임감이 강하고 안전을 중요시하며, 신뢰할 수 있는 대상을 찾습니다.'
  },
  7: {
    name: 'The Enthusiast',
    nickname: '열정가',
    color: 'bg-violet-500',
    shortDescription: '바쁘고 재미를 추구하는 유형',
    description: '새로운 경험과 즐거움을 추구하며, 고통을 피하려는 경향이 있습니다.'
  },
  8: {
    name: 'The Challenger',
    nickname: '도전가',
    color: 'bg-pink-600',
    shortDescription: '강력하고 주도적인 유형',
    description: '자신의 통제력을 중요시하며, 강한 의지로 환경을 변화시키려 합니다.'
  },
  9: {
    name: 'The Peacemaker',
    nickname: '평화가',
    color: 'bg-slate-500',
    shortDescription: '느긋하고 조화를 추구하는 유형',
    description: '갈등을 피하고 평화를 유지하려 하며, 타인과 융합하려는 성향이 있습니다.'
  }
};

// 36 Questions (4 per type for the demo)
const QUESTIONS = [
  // Type 1
  { id: 1, type: 1, text: "나는 일을 할 때 원칙을 지키고 완벽하게 처리하려고 노력한다." },
  { id: 2, type: 1, text: "나는 스스로에게 엄격하며, 실수를 용납하기 어렵다." },
  { id: 3, type: 1, text: "나는 상황이 '올바르게' 돌아가지 않을 때 내적으로 비판적인 목소리가 커진다." },
  { id: 4, type: 1, text: "나는 도덕적 우위와 공정함을 유지하는 것이 매우 중요하다." },
  // Type 2
  { id: 5, type: 2, text: "나는 다른 사람들의 필요를 그들 자신보다 먼저 알아차리곤 한다." },
  { id: 6, type: 2, text: "나는 타인에게 도움이 될 때 나의 가치를 가장 크게 느낀다." },
  { id: 7, type: 2, text: "나는 관계에서 거절당하거나 필요 없는 존재가 되는 것이 두렵다." },
  { id: 8, type: 2, text: "나는 내 자신의 욕구보다 타인의 감정을 먼저 살피는 편이다." },
  // Type 3
  { id: 9, type: 3, text: "나는 목표를 달성하고 성과를 내는 것이 인생에서 매우 중요하다." },
  { id: 10, type: 3, text: "나는 효율성을 중시하며, 시간 낭비를 극도로 싫어한다." },
  { id: 11, type: 3, text: "나는 다른 사람들에게 유능하고 성공적인 사람으로 보이고 싶다." },
  { id: 12, type: 3, text: "나는 감정에 빠져 일의 진행을 늦추는 것을 좋아하지 않는다." },
  // Type 4
  { id: 13, type: 4, text: "나는 남들과 다른 나만의 고유한 감정과 취향을 중요하게 여긴다." },
  { id: 14, type: 4, text: "나는 종종 알 수 없는 우울감이나 멜랑콜리한 기분에 빠지곤 한다." },
  { id: 15, type: 4, text: "나는 평범한 삶보다는 특별하고 의미 있는 삶을 살고 싶다." },
  { id: 16, type: 4, text: "나는 내 감정을 깊이 이해해주는 사람이 없다고 느낄 때가 많다." },
  // Type 5
  { id: 17, type: 5, text: "나는 감정적인 상황보다는 논리적이고 지적인 분석을 선호한다." },
  { id: 18, type: 5, text: "나는 나만의 시간과 공간이 침해받을 때 큰 스트레스를 받는다." },
  { id: 19, type: 5, text: "나는 행동하기 전에 충분한 정보를 수집하고 관찰하는 것을 좋아한다." },
  { id: 20, type: 5, text: "나는 타인에게 의존하기보다 스스로 해결할 수 있는 능력을 키우고 싶다." },
  // Type 6
  { id: 21, type: 6, text: "나는 최악의 상황을 대비해 미리 계획을 세우는 습관이 있다." },
  { id: 22, type: 6, text: "나는 권위 있는 사람이나 믿을 수 있는 그룹에 소속될 때 안정감을 느낀다." },
  { id: 23, type: 6, text: "나는 결정을 내릴 때 신중하며, 주변 사람들의 조언을 구하곤 한다." },
  { id: 24, type: 6, text: "나는 불확실한 미래에 대한 막연한 불안감을 자주 느낀다." },
  // Type 7
  { id: 25, type: 7, text: "나는 지루한 것을 참기 힘들며, 항상 새롭고 흥미로운 일을 찾는다." },
  { id: 26, type: 7, text: "나는 부정적인 감정을 피하고 긍정적인 면을 보려고 노력한다." },
  { id: 27, type: 7, text: "나는 한 가지 일에 얽매이기보다 다양한 가능성을 열어두고 싶다." },
  { id: 28, type: 7, text: "나는 계획을 세우는 것 자체를 즐기며, 미래에 대한 낙관적인 기대를 가지고 있다." },
  // Type 8
  { id: 29, type: 8, text: "나는 상황을 주도하고 통제할 때 편안함을 느낀다." },
  { id: 30, type: 8, text: "나는 부당한 대우를 받거나 약자가 괴롭힘 당하는 것을 보면 참지 못한다." },
  { id: 31, type: 8, text: "나는 나의 약점을 드러내는 것을 싫어하며, 강한 모습을 유지하려 한다." },
  { id: 32, type: 8, text: "나는 직설적으로 말하는 편이며, 돌려 말하는 것을 답답해한다." },
  // Type 9
  { id: 33, type: 9, text: "나는 갈등을 일으키기보다는 평화를 유지하고 양보하는 편이다." },
  { id: 34, type: 9, text: "나는 다른 사람의 의견에 잘 따르며, '아니오'라고 말하기 어려워한다." },
  { id: 35, type: 9, text: "나는 종종 내가 무엇을 진정으로 원하는지 모를 때가 있다." },
  { id: 36, type: 9, text: "나는 변화보다는 익숙하고 편안한 상태를 선호한다." },
];

// --- Gemini Service ---

const getEnneagramAnalysis = async (typeNum) => {
  const typeInfo = TYPE_DETAILS[typeNum];
  const apiKey = ""; // API Key injected by environment
  
  const systemPrompt = `You are an expert Enneagram Psychologist. Your goal is to analyze the user's personality type and provide deep, insightful, and empathetic feedback in Korean.`;
  
  const userPrompt = `
    Analyze Enneagram Type ${typeNum} (${typeInfo.name}, ${typeInfo.nickname}).
    Provide a detailed psychological report in JSON format with the following structure:
    {
      "overview": "A comprehensive paragraph (3-4 sentences) explaining the core psychological mechanism, basic desire, and fear of this type in Korean.",
      "strengths": ["Strength 1 (Korean)", "Strength 2 (Korean)", "Strength 3 (Korean)", "Strength 4 (Korean)"],
      "weaknesses": ["Weakness/Shadow 1 (Korean)", "Weakness/Shadow 2 (Korean)", "Weakness/Shadow 3 (Korean)", "Weakness/Shadow 4 (Korean)"],
      "growthAdvice": "Practical and warm advice for personal growth and self-integration in Korean (2-3 sentences).",
      "relationshipAdvice": "Specific advice for how this type can improve their relationships and communication with others in Korean (2-3 sentences)."
    }
    Ensure the tone is professional yet warm and supportive.
  `;

  try {
    const response = await fetch(
      `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`,
      {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          contents: [{ parts: [{ text: userPrompt }] }],
          systemInstruction: { parts: [{ text: systemPrompt }] },
          generationConfig: {
            responseMimeType: "application/json",
            temperature: 0.7,
          }
        }),
      }
    );

    if (!response.ok) throw new Error('API Request Failed');

    const data = await response.json();
    const resultText = data.candidates?.[0]?.content?.parts?.[0]?.text;
    
    if (!resultText) throw new Error('No content generated');
    
    return JSON.parse(resultText);
  } catch (error) {
    console.error('Gemini API Error:', error);
    // Fallback data in case of API failure
    return {
      overview: `${typeInfo.nickname}(${typeInfo.name}) 유형은 ${typeInfo.description} 이 유형은 자신의 신념과 가치를 매우 중요하게 여기며, 세상을 더 나은 곳으로 만들고자 하는 깊은 열망을 가지고 있습니다. 때로는 강박적인 완벽주의 성향이 나타날 수 있습니다.`,
      strengths: ["높은 책임감과 성실함", "명확한 원칙과 기준", "세상을 개선하려는 의지", "논리적이고 객관적인 태도"],
      weaknesses: ["지나친 비판적 사고", "유연성 부족", "자신과 타인에 대한 엄격함", "분노를 억압하는 경향"],
      growthAdvice: "완벽하지 않아도 괜찮다는 사실을 받아들이세요. 자신에게 조금 더 관대해지는 연습이 필요합니다.",
      relationshipAdvice: "상대방의 실수나 결점을 지적하기보다 그들의 노력과 의도를 먼저 인정해주세요."
    };
  }
};

// --- Components ---

const ProgressBar = ({ current, total }) => {
  const progress = (current / total) * 100;
  return (
    <div className="w-full bg-slate-100 rounded-full h-3 mb-8 overflow-hidden">
      <div 
        className="bg-indigo-600 h-full rounded-full transition-all duration-500 ease-out shadow-[0_0_12px_rgba(79,70,229,0.4)] relative" 
        style={{ width: `${progress}%` }}
      >
        <div className="absolute top-0 left-0 w-full h-full bg-white/20 animate-pulse"></div>
      </div>
    </div>
  );
};

const Layout = ({ children }) => (
  <div className="min-h-screen bg-slate-50 flex flex-col items-center py-8 md:py-12 px-4 selection:bg-indigo-100 font-sans text-slate-800">
    <div className="max-w-3xl w-full">
      <header className="text-center mb-10 md:mb-12">
        <div className="flex items-center justify-center gap-3 mb-2">
          <Orbit className="w-10 h-10 text-indigo-600 animate-spin-slow" />
          <h1 className="text-3xl md:text-4xl font-black text-slate-900 tracking-tight">
            Enneagram Insight
          </h1>
        </div>
        <p className="text-slate-500 font-medium text-sm md:text-base">당신의 내면 지도를 완성하는 인공지능 심리 분석</p>
      </header>
      <main className="bg-white rounded-[2.5rem] shadow-[0_20px_50px_rgba(0,0,0,0.05)] p-6 md:p-12 border border-white/60 backdrop-blur-sm transition-all duration-500 relative overflow-hidden">
        <div className="absolute top-0 left-0 w-full h-2 bg-gradient-to-r from-indigo-500 via-purple-500 to-pink-500"></div>
        {children}
      </main>
      <footer className="mt-12 text-center text-slate-400 text-xs md:text-sm font-medium">
        &copy; 2025 Enneagram Insight Lab. Powered by Gemini 2.5 Flash.
      </footer>
    </div>
    <style>{`
      @keyframes spin-slow {
        from { transform: rotate(0deg); }
        to { transform: rotate(360deg); }
      }
      .animate-spin-slow {
        animation: spin-slow 12s linear infinite;
      }
    `}</style>
  </div>
);

// --- Pages ---

const LandingPage = () => {
  const navigate = useNavigate();
  return (
    <div className="text-center animate-in fade-in zoom-in duration-700">
      <div className="mb-10 flex justify-center">
        <div className="grid grid-cols-3 gap-3 w-56 md:w-64">
          {[1, 2, 3, 4, 5, 6, 7, 8, 9].map((type) => (
            <div 
              key={type} 
              className={`aspect-square rounded-2xl ${TYPE_DETAILS[type].color} shadow-lg shadow-indigo-100 flex items-center justify-center text-white font-bold text-xl md:text-2xl hover:scale-110 transition-transform cursor-default select-none bg-opacity-90 backdrop-blur-sm`}
            >
              {type}
            </div>
          ))}
        </div>
      </div>
      <h2 className="text-2xl md:text-3xl font-bold text-slate-800 mb-6 tracking-tight break-keep">
        당신은 어떤 <span className="text-indigo-600">빛</span>과 <span className="text-pink-500">그림자</span>를 갖고 있나요?
      </h2>
      <p className="text-slate-600 mb-12 leading-relaxed text-base md:text-lg px-2 break-keep">
        애니어그램은 단순한 테스트를 넘어, 당신의 무의식적 동기를 탐구하는 도구입니다.<br/>
        <span className="font-bold text-indigo-600">엄선된 정밀 심화 문항</span>을 통해<br className="hidden md:block"/>
        AI가 분석한 당신만의 고유한 심리 지도를 발견해보세요.
      </p>
      <button 
        onClick={() => navigate('/test')}
        className="bg-indigo-600 hover:bg-indigo-700 text-white font-extrabold py-5 px-10 md:px-14 rounded-[2rem] transition-all shadow-xl shadow-indigo-200/50 flex items-center justify-center gap-4 mx-auto text-lg md:text-xl active:scale-95 group w-full md:w-auto"
      >
        심층 검사 시작
        <ArrowRight className="w-5 h-5 group-hover:translate-x-1 transition-transform" />
      </button>
    </div>
  );
};

const TestPage = ({ onComplete }) => {
  const [currentIdx, setCurrentIdx] = useState(0);
  const [answers, setAnswers] = useState({});
  const navigate = useNavigate();

  // Shuffle questions slightly or just use fixed order? Fixed for consistency in demo.
  const currentQuestion = QUESTIONS[currentIdx];

  const handleAnswer = (value) => {
    const newAnswers = { ...answers, [currentQuestion.id]: value };
    setAnswers(newAnswers);

    if (currentIdx < QUESTIONS.length - 1) {
      setCurrentIdx(prev => prev + 1);
      window.scrollTo(0, 0); // Scroll to top for mobile
    } else {
      calculateResult(newAnswers);
    }
  };

  const calculateResult = (finalAnswers) => {
    const scores = { 1: 0, 2: 0, 3: 0, 4: 0, 5: 0, 6: 0, 7: 0, 8: 0, 9: 0 };
    
    QUESTIONS.forEach(q => {
      // Map answer 1-5 to score. 
      // Assuming straightforward scoring: 5 = strong correlation
      scores[q.type] += finalAnswers[q.id] || 0;
    });

    let dominantType = 1;
    let maxScore = -1;
    
    // Find highest score
    Object.keys(scores).forEach(type => {
      const typeNum = parseInt(type);
      if (scores[typeNum] > maxScore) {
        maxScore = scores[typeNum];
        dominantType = typeNum;
      }
    });

    onComplete({ scores, dominantType });
    navigate('/result');
  };

  return (
    <div className="animate-in slide-in-from-right duration-500 min-h-[500px] flex flex-col">
      <div className="flex justify-between items-center mb-6">
        <span className="text-xs md:text-sm font-bold text-indigo-600 bg-indigo-50 px-4 py-1.5 rounded-full uppercase tracking-wider">
          Question {currentIdx + 1} / {QUESTIONS.length}
        </span>
        <button 
          onClick={() => navigate('/')} 
          className="text-slate-300 hover:text-red-500 transition-colors p-2"
          aria-label="Exit"
        >
          <XCircle className="w-8 h-8" />
        </button>
      </div>
      
      <ProgressBar current={currentIdx + 1} total={QUESTIONS.length} />
      
      <div className="flex-grow flex items-center justify-center mb-8 md:mb-12 px-2">
        <h3 className="text-xl md:text-3xl font-bold text-slate-800 text-center leading-[1.5] tracking-tight break-keep">
          {currentQuestion.text}
        </h3>
      </div>

      <div className="grid grid-cols-1 gap-3 md:gap-4">
        {[
          { label: '전혀 그렇지 않다', value: 1, color: 'hover:border-red-200 hover:bg-red-50 text-slate-500' },
          { label: '그렇지 않다', value: 2, color: 'hover:border-orange-200 hover:bg-orange-50 text-slate-600' },
          { label: '보통이다', value: 3, color: 'hover:border-gray-200 hover:bg-gray-50 text-slate-600' },
          { label: '그렇다', value: 4, color: 'hover:border-blue-200 hover:bg-blue-50 text-slate-700' },
          { label: '매우 그렇다', value: 5, color: 'hover:border-indigo-200 hover:bg-indigo-50 text-indigo-700' },
        ].map((opt) => (
          <button
            key={opt.value}
            onClick={() => handleAnswer(opt.value)}
            className={`w-full py-4 md:py-5 px-6 md:px-8 rounded-2xl font-bold border-2 border-slate-100 transition-all text-left flex justify-between items-center group active:scale-[0.98] ${opt.color}`}
          >
            <span className="text-sm md:text-base">{opt.label}</span>
            <div className={`h-5 w-5 md:h-6 md:w-6 rounded-full border-2 border-slate-200 group-hover:border-current transition-all flex items-center justify-center`}>
              <div className="w-2.5 h-2.5 rounded-full bg-current opacity-0 group-hover:opacity-100 transition-opacity"></div>
            </div>
          </button>
        ))}
      </div>
      
      {currentIdx > 0 && (
        <button 
          onClick={() => setCurrentIdx(prev => prev - 1)}
          className="mt-8 text-slate-400 hover:text-indigo-600 text-sm font-bold transition-colors flex items-center gap-2 mx-auto"
        >
          <ChevronLeft className="w-4 h-4" /> 이전 질문 수정하기
        </button>
      )}
    </div>
  );
};

const ResultPage = ({ result }) => {
  const [analysis, setAnalysis] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const navigate = useNavigate();

  // Calculate max score for bar chart normalization
  const maxPossibleScore = useMemo(() => {
    // We assume 4 questions per type, max score 5 per question = 20
    return 20; 
  }, []);

  useEffect(() => {
    if (!result) {
      navigate('/');
      return;
    }

    const fetchAnalysis = async () => {
      try {
        setLoading(true);
        // Add a small artificial delay for UX if the API is too fast
        const [data] = await Promise.all([
          getEnneagramAnalysis(result.dominantType),
          new Promise(resolve => setTimeout(resolve, 1500))
        ]);
        setAnalysis(data);
      } catch (err) {
        console.error(err);
        setError('분석 데이터를 생성하는 중 오류가 발생했습니다.');
      } finally {
        setLoading(false);
      }
    };

    fetchAnalysis();
  }, [result, navigate]);

  if (!result) return null;

  const typeInfo = TYPE_DETAILS[result.dominantType];

  return (
    <div className="animate-in fade-in slide-in-from-bottom duration-1000">
      <div className="text-center mb-10 md:mb-14">
        <div className={`inline-flex items-center gap-2 px-6 py-2 rounded-full text-white text-xs md:text-sm font-black mb-6 shadow-lg ${typeInfo.color}`}>
          <Gem className="w-4 h-4" />
          ESTABLISHED TYPE {result.dominantType}
        </div>
        <h2 className="text-4xl md:text-6xl font-black text-slate-900 mb-4 tracking-tighter">
          {typeInfo.name} <span className="text-indigo-600 block md:inline text-3xl md:text-5xl mt-2 md:mt-0">({typeInfo.nickname})</span>
        </h2>
        <p className="text-slate-500 text-lg md:text-xl font-medium italic break-keep px-4">"{typeInfo.shortDescription}"</p>
      </div>

      <div className="bg-slate-50/80 rounded-[2rem] p-6 md:p-10 mb-12 border border-slate-100 overflow-x-auto">
        <h3 className="text-lg md:text-xl font-bold text-slate-800 mb-8 flex items-center gap-3">
          <PieChart className="w-6 h-6 text-indigo-500" /> 유형별 에너지 분포
        </h3>
        <div className="flex items-end justify-between h-40 md:h-48 gap-2 min-w-[300px]">
          {[1, 2, 3, 4, 5, 6, 7, 8, 9].map(type => {
            const score = result.scores[type];
            // If we have variable questions per type, calculate max per type
            // Here we assume uniform distribution (4 questions * 5 points = 20)
            const percentage = Math.min((score / maxPossibleScore) * 100, 100); 
            const isDominant = type === result.dominantType;
            return (
              <div key={type} className="flex flex-col items-center flex-1 group relative">
                <div className="absolute -top-8 text-[10px] md:text-xs font-bold text-slate-500 opacity-0 group-hover:opacity-100 transition-opacity whitespace-nowrap bg-white px-2 py-1 rounded shadow-sm border">
                  {Math.round(score)}점
                </div>
                <div 
                  className={`w-full max-w-[40px] rounded-t-lg transition-all duration-1000 ease-out hover:brightness-110 ${isDominant ? TYPE_DETAILS[type].color : 'bg-slate-200'}`}
                  style={{ height: `${Math.max(percentage, 5)}%` }}
                ></div>
                <span className={`text-xs md:text-sm mt-3 font-black ${isDominant ? 'text-indigo-600 scale-125 transform' : 'text-slate-400'}`}>{type}</span>
              </div>
            );
          })}
        </div>
      </div>

      {loading ? (
        <div className="py-24 text-center">
          <div className="relative inline-flex items-center justify-center w-24 h-24 mb-8">
            <div className="absolute inset-0 rounded-full border-4 border-indigo-50"></div>
            <div className="absolute inset-0 rounded-full border-4 border-indigo-600 border-t-transparent animate-spin"></div>
            <Orbit className="w-8 h-8 text-indigo-600 animate-spin-slow" />
          </div>
          <p className="text-xl font-bold text-slate-700 mb-2 animate-pulse">심층 분석 리포트 생성 중...</p>
          <p className="text-slate-400 text-sm">Gemini AI가 당신의 응답 패턴을 분석하고 있습니다.</p>
        </div>
      ) : error ? (
        <div className="bg-red-50 p-8 rounded-3xl text-red-700 text-center border border-red-100">
          <AlertCircle className="w-10 h-10 mx-auto mb-4 text-red-500" />
          <p className="text-lg font-bold mb-4">{error}</p>
          <button 
            onClick={() => window.location.reload()}
            className="bg-red-600 text-white px-8 py-3 rounded-2xl font-bold hover:bg-red-700 transition-colors"
          >
            다시 시도하기
          </button>
        </div>
      ) : analysis && (
        <div className="space-y-8 md:space-y-12 animate-in fade-in duration-1000">
          <section className="relative">
            <div className="absolute -left-6 top-0 bottom-0 w-1 bg-indigo-600 rounded-full hidden md:block"></div>
            <h3 className="text-2xl font-black text-slate-900 mb-5">심리학적 분석 (Overview)</h3>
            <p className="text-slate-600 leading-loose text-lg bg-white p-6 rounded-2xl border border-slate-100 shadow-sm break-keep">
              {analysis.overview}
            </p>
          </section>

          <div className="grid md:grid-cols-2 gap-6 md:gap-8">
            <section className="bg-emerald-50/60 p-6 md:p-8 rounded-[2rem] border border-emerald-100/50 hover:bg-emerald-50 transition-colors">
              <h3 className="text-xl font-black text-emerald-900 mb-6 flex items-center gap-3">
                <Gem className="w-6 h-6 text-emerald-500" /> 핵심 강점
              </h3>
              <ul className="space-y-4">
                {analysis.strengths.map((s, i) => (
                  <li key={i} className="text-emerald-800 font-medium flex gap-3 text-lg items-start">
                    <Check className="w-5 h-5 text-emerald-500 shrink-0 mt-1" strokeWidth={3} /> 
                    <span className="break-keep">{s}</span>
                  </li>
                ))}
              </ul>
            </section>
            <section className="bg-rose-50/60 p-6 md:p-8 rounded-[2rem] border border-rose-100/50 hover:bg-rose-50 transition-colors">
              <h3 className="text-xl font-black text-rose-900 mb-6 flex items-center gap-3">
                <Ghost className="w-6 h-6 text-rose-500" /> 그림자 (Shadow)
              </h3>
              <ul className="space-y-4">
                {analysis.weaknesses.map((w, i) => (
                  <li key={i} className="text-rose-800 font-medium flex gap-3 text-lg items-start">
                    <AlertCircle className="w-5 h-5 text-rose-400 shrink-0 mt-1" strokeWidth={2.5} />
                    <span className="break-keep">{w}</span>
                  </li>
                ))}
              </ul>
            </section>
          </div>

          <section className="bg-gradient-to-br from-indigo-600 to-violet-700 text-white p-8 md:p-10 rounded-[2.5rem] shadow-2xl shadow-indigo-200 relative overflow-hidden">
            <div className="absolute top-0 right-0 w-64 h-64 bg-white opacity-5 rounded-full -translate-y-1/2 translate-x-1/3 blur-3xl"></div>
            <h3 className="text-2xl font-black mb-6 flex items-center gap-3 relative z-10">
              <TrendingUp className="w-7 h-7" /> 성장과 통합을 위한 제언
            </h3>
            <p className="text-indigo-50 leading-loose text-lg relative z-10 break-keep">
              {analysis.growthAdvice}
            </p>
          </section>

          <section className="bg-slate-900 text-slate-200 p-8 md:p-10 rounded-[2.5rem] relative overflow-hidden">
             <div className="absolute bottom-0 left-0 w-64 h-64 bg-indigo-500 opacity-10 rounded-full translate-y-1/3 -translate-x-1/3 blur-3xl"></div>
            <h3 className="text-2xl font-black mb-6 flex items-center gap-3 text-white relative z-10">
              <Users className="w-7 h-7 text-indigo-400" /> 관계 및 소통 가이드
            </h3>
            <p className="text-slate-300 leading-loose text-lg relative z-10 break-keep">
              {analysis.relationshipAdvice}
            </p>
          </section>

          <div className="pt-8 border-t border-slate-200 flex flex-col md:flex-row gap-4">
            <button 
              onClick={() => navigate('/')}
              className="flex-1 bg-white border border-slate-200 hover:bg-slate-50 text-slate-700 font-extrabold py-4 md:py-5 rounded-3xl transition-all"
            >
              처음으로 돌아가기
            </button>
            <button 
              onClick={() => window.print()}
              className="flex-1 bg-indigo-600 hover:bg-indigo-700 text-white font-extrabold py-4 md:py-5 rounded-3xl transition-all shadow-xl shadow-indigo-100 flex items-center justify-center gap-3"
            >
              <Download className="w-5 h-5" /> 결과 저장 (PDF)
            </button>
          </div>
        </div>
      )}
    </div>
  );
};

// --- Main App ---

const App = () => {
  const [result, setResult] = useState(null);

  return (
    <Router>
      <Layout>
        <Routes>
          <Route path="/" element={<LandingPage />} />
          <Route path="/test" element={<TestPage onComplete={setResult} />} />
          <Route path="/result" element={<ResultPage result={result} />} />
        </Routes>
      </Layout>
    </Router>
  );
};

export default App;
