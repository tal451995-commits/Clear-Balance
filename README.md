# ---
.חשבון ברור היא מערכת חכמה ומתקדמת לניהול כלכלי אישי, מעקב חובות והלוואות, תכנון תקציב חודשי ומחשבון כסף פנוי. המערכת מעניקה תמונת מצב פיננסית מדויקת בזמן אמת, כולל ניהול הכנסות והוצאות, מעקב יעדי חיסכון, תובנות מותאמות אישית, גרפים מפורטים ואפשרות לייצוא נתונים — והכל בממשק בעברית מלאה, נוח
import React, { useState, useEffect, useMemo } from 'react';
import { 
  Wallet, 
  TrendingUp, 
  TrendingDown, 
  PiggyBank, 
  HandCoins, 
  Plus, 
  Calendar, 
  Filter, 
  PieChart as PieIcon, 
  BarChart3, 
  Download, 
  Search, 
  CheckCircle, 
  AlertCircle, 
  ArrowUpRight, 
  ArrowDownRight, 
  Building2, 
  CreditCard, 
  Smartphone, 
  ChevronRight, 
  Sparkles, 
  Calculator, 
  Trash2, 
  Edit, 
  FileText,
  DollarSign,
  Info
} from 'lucide-react';

const INITIAL_DATA = {
  accounts: [
    { id: '1', name: '讞砖讘讜谉 注讜"砖 注讬拽专讬', type: 'bank', balance: 14250 },
    { id: '2', name: '讗专谞拽 诪讝讜诪谉', type: 'cash', balance: 850 },
    { id: '3', name: 'Bit / PayBox', type: 'digital', balance: 1200 }
  ],
  transactions: [
    { id: 't1', title: '诪砖讻讜专转', amount: 16500, type: 'income', category: '砖讻专', accountId: '1', date: '2026-10-01', paymentMethod: 'bank_transfer', status: 'completed' },
    { id: 't2', title: '住讜驻专诪专拽讟 - 砖讜驻专住诇', amount: 840, type: 'expense', category: '诪讝讜谉 讜住讜驻专', accountId: '1', date: '2026-10-03', paymentMethod: 'card', status: 'completed' },
    { id: 't3', title: '砖讻讬专讜转', amount: 4800, type: 'expense', category: '砖讻讬专讜转 讜诪砖讻谞转讛', accountId: '1', date: '2026-10-05', paymentMethod: 'bank_transfer', status: 'completed' },
    { id: 't4', title: '讚诇拽 - 驻讝', amount: 320, type: 'expense', category: '转讞讘讜专讛 讜讚诇拽', accountId: '1', date: '2026-10-06', paymentMethod: 'card', status: 'completed' },
    { id: 't5', title: '驻专讜讬拽讟 驻专讬诇谞住', amount: 2400, type: 'income', category: '注住拽 注爪诪讗讬', accountId: '3', date: '2026-10-07', paymentMethod: 'bit', status: 'completed' },
    { id: 't6', title: '诪住注讚讛 - 讙'讬专祝', amount: 280, type: 'expense', category: '诪住注讚讜转 讜讘讬诇讜讬讬诐', accountId: '1', date: '2026-10-08', paymentMethod: 'card', status: 'completed' }
  ],
  categories: [
    { name: '诪讝讜谉 讜住讜驻专', budget: 3000 },
    { name: '砖讻讬专讜转 讜诪砖讻谞转讛', budget: 4800 },
    { name: '诪住注讚讜转 讜讘讬诇讜讬讬诐', budget: 1200 },
    { name: '转讞讘讜专讛 讜讚诇拽', budget: 1000 },
    { name: '讞砖诪诇 讜诪讬诐', budget: 800 },
    { name: '拽谞讬讜转', budget: 1500 }
  ],
  loans: [
    { id: 'l1', personName: '讬讜住讬 讻讛谉', type: 'given', principalAmount: 5000, remainingAmount: 2000, startDate: '2026-08-15', dueDate: '2026-11-01', status: 'active', notes: '讛诇讜讜讗讛 诇讞讜驻砖讛' },
    { id: 'l2', personName: '讚谞讬 诇讜讬', type: 'taken', principalAmount: 3000, remainingAmount: 3000, startDate: '2026-09-10', dueDate: '2026-12-01', status: 'active', notes: '注讝专讛 讘专讻讬砖转 爪讬讜讚' }
  ],
  savings: [
    { id: 's1', name: '拽专谉 讞讬专讜诐', targetAmount: 30000, currentAmount: 18500, targetDate: '2027-06-30' },
    { id: 's2', name: '讞讜驻砖讛 讘讬讜讜谉', targetAmount: 8000, currentAmount: 5200, targetDate: '2027-05-01' }
  ]
};
export default function App() {
  const [data, setData] = useState(() => {
    const saved = localStorage.getItem('clear_account_db');
    return saved ? JSON.parse(saved) : INITIAL_DATA;
  });

  const [activeTab, setActiveTab] = useState('dashboard');
  const [modalType, setModalType] = useState(null); // 'transaction', 'loan', 'saving'
  const [filterCategory, setFilterCategory] = useState('all');
  const [searchTerm, setSearchTerm] = useState('');

  // Save to LocalStorage
  useEffect(() => {
    localStorage.setItem('clear_account_db', JSON.stringify(data));
  }, [data]);

  const metrics = useMemo(() => {
    const totalBalance = data.accounts.reduce((acc, curr) => acc + Number(curr.balance), 0);
    
    const currentMonthIncome = data.transactions
      .filter(t => t.type === 'income' && t.status === 'completed')
      .reduce((acc, curr) => acc + Number(curr.amount), 0);

    const currentMonthExpense = data.transactions
      .filter(t => t.type === 'expense' && t.status === 'completed')
      .reduce((acc, curr) => acc + Number(curr.amount), 0);

    const moneyLent = data.loans
      .filter(l => l.type === 'given' && l.status === 'active')
      .reduce((acc, curr) => acc + Number(curr.remainingAmount), 0);

    const moneyOwed = data.loans
      .filter(l => l.type === 'taken' && l.status === 'active')
      .reduce((acc, curr) => acc + Number(curr.remainingAmount), 0);

    const totalAllocatedSavings = data.savings.reduce((acc, curr) => acc + Number(curr.currentAmount), 0);

    const netMonthly = currentMonthIncome - currentMonthExpense;
    const freeCapital = totalBalance - moneyOwed - (totalAllocatedSavings * 0.1);

    return {
      totalBalance,
      currentMonthIncome,
      currentMonthExpense,
      moneyLent,
      moneyOwed,
      totalAllocatedSavings,
      netMonthly,
      freeCapital
    };
  }, [data]);

  const handleAddTransaction = (newTx) => {
    const tx = { ...newTx, id: 't_' + Date.now(), status: 'completed' };
    setData(prev => {
      // Update account balance
      const updatedAccounts = prev.accounts.map(acc => {
        if (acc.id === tx.accountId) {
          const change = tx.type === 'income' ? Number(tx.amount) : -Number(tx.amount);
          return { ...acc, balance: Number(acc.balance) + change };
        }
        return acc;
      });

      return {
        ...prev,
        transactions: [tx, ...prev.transactions],
        accounts: updatedAccounts
      };
    });
    setModalType(null);
  };

  const handleAddLoan = (newLoan) => {
    const loan = { 
      ...newLoan, 
      id: 'l_' + Date.now(), 
      remainingAmount: Number(newLoan.principalAmount),
      status: 'active' 
    };
    setData(prev => ({ ...prev, loans: [loan, ...prev.loans] }));
    setModalType(null);
  };
  const handleAddSaving = (newSaving) => {
    const saving = { 
      ...newSaving, 
      id: 's_' + Date.now(), 
      currentAmount: Number(newSaving.currentAmount || 0) 
    };
    setData(prev => ({ ...prev, savings: [saving, ...prev.savings] }));
    setModalType(null);
  };

  const handleDeleteTransaction = (id) => {
    setData(prev => ({
      ...prev,
      transactions: prev.transactions.filter(t => t.id !== id)
    }));
  };

  return (
    <div className="min-h-screen bg-[#FDFBF7] text-slate-800 font-sans dir-rtl" dir="rtl">
      {/* Top Header */}
      <header className="bg-white/80 backdrop-blur-md border-b border-emerald-900/10 sticky top-0 z-30 px-4 py-3 md:px-8">
        <div className="max-w-7xl mx-auto flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="bg-gradient-to-tr from-emerald-800 to-teal-600 p-2.5 rounded-2xl shadow-sm text-white">
              <Wallet className="w-6 h-6" />
            </div>
            <div>
              <h1 className="text-xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-emerald-900 to-teal-800">
                讞砖讘讜谉 讘专讜专
              </h1>
              <p className="text-xs text-emerald-800/60 font-medium">谞讬讛讜诇 驻讬谞谞住讬 讞讻诐 讜讬讜拽专转讬</p>
            </div>
          </div>

          <div className="hidden md:flex items-center gap-2">
            <NavButton active={activeTab === 'dashboard'} onClick={() => setActiveTab('dashboard')} icon={<BarChart3 className="w-4 h-4" />} label="讚砖讘讜专讚" />
            <NavButton active={activeTab === 'transactions'} onClick={() => setActiveTab('transactions')} icon={<FileText className="w-4 h-4" />} label="转谞讜注讜转 讜转拽爪讬讘" />
            <NavButton active={activeTab === 'loans'} onClick={() => setActiveTab('loans')} icon={<HandCoins className="w-4 h-4" />} label="讛诇讜讜讗讜转 讜讞讜讘讜转" />
            <NavButton active={activeTab === 'savings'} onClick={() => setActiveTab('savings')} icon={<PiggyBank className="w-4 h-4" />} label="讞讬住讻讜谉" />
            <NavButton active={activeTab === 'forecast'} onClick={() => setActiveTab('forecast')} icon={<Calculator className="w-4 h-4" />} label="转讞讝讬转 讜讻住祝 驻谞讜讬" />
          </div>
          <button 
            onClick={() => setModalType('transaction')}
            className="flex items-center gap-2 bg-emerald-800 hover:bg-emerald-900 text-white text-sm font-semibold px-4 py-2.5 rounded-xl shadow-md transition-all active:scale-95"
          >
            <Plus className="w-4 h-4" />
            <span>转谞讜注讛 讞讚砖讛</span>
          </button>
        </div>
      </header>

      {/* Main Content Body */}
      <main className="max-w-7xl mx-auto px-4 py-6 md:px-8 mb-24 md:mb-12">
        {activeTab === 'dashboard' && <DashboardView metrics={metrics} data={data} setModalType={setModalType} setActiveTab={setActiveTab} />}
        {activeTab === 'transactions' && <TransactionsView data={data} onDelete={handleDeleteTransaction} filterCategory={filterCategory} setFilterCategory={setFilterCategory} searchTerm={searchTerm} setSearchTerm={setSearchTerm} />}
        {activeTab === 'loans' && <LoansView data={data} setData={setData} setModalType={setModalType} />}
        {activeTab === 'savings' && <SavingsView data={data} setData={setData} setModalType={setModalType} />}
        {activeTab === 'forecast' && <ForecastView metrics={metrics} data={data} />}
      </main>

      {/* Mobile Bottom Navigation */}
      <nav className="md:hidden fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-lg border-t border-emerald-900/10 px-3 py-2 z-30 shadow-lg">
        <div className="flex justify-around items-center">
          <MobileNavItem active={activeTab === 'dashboard'} onClick={() => setActiveTab('dashboard')} icon={<BarChart3 />} label="专讗砖讬" />
          <MobileNavItem active={activeTab === 'transactions'} onClick={() => setActiveTab('transactions')} icon={<FileText />} label="转谞讜注讜转" />
          <MobileNavItem active={activeTab === 'loans'} onClick={() => setActiveTab('loans')} icon={<HandCoins />} label="讞讜讘讜转" />
          <MobileNavItem active={activeTab === 'savings'} onClick={() => setActiveTab('savings')} icon={<PiggyBank />} label="讞讬住讻讜谉" />
          <MobileNavItem active={activeTab === 'forecast'} onClick={() => setActiveTab('forecast')} icon={<Calculator />} label="转讞讝讬转" />
        </div>
      </nav>

      {/* Modals */}
      {modalType === 'transaction' && <TransactionModal onClose={() => setModalType(null)} onSave={handleAddTransaction} accounts={data.accounts} categories={data.categories} />}
      {modalType === 'loan' && <LoanModal onClose={() => setModalType(null)} onSave={handleAddLoan} />}
      {modalType === 'saving' && <SavingModal onClose={() => setModalType(null)} onSave={handleAddSaving} />}
    </div>
  );
}

function NavButton({ active, onClick, icon, label }) {
  return (
    <button
      onClick={onClick}
      className={`flex items-center gap-2 px-4 py-2 rounded-xl text-sm font-semibold transition-all ${
        active 
          ? 'bg-emerald-900/10 text-emerald-900 shadow-sm' 
          : 'text-slate-600 hover:bg-slate-100 hover:text-slate-900'
      }`}
    >
      {icon}
      <span>{label}</span>
    </button>
  );
}

function MobileNavItem({ active, onClick, icon, label }) {
  return (
    <button
      onClick={onClick}
      className={`flex flex-col items-center gap-1 p-1.5 transition-all ${
        active ? 'text-emerald-800 font-bold scale-105' : 'text-slate-400 font-medium'
      }`}
    >
      {React.cloneElement(icon, { className: 'w-5 h-5' })}
      <span className="text-[10px]">{label}</span>
    </button>
  );
}

function DashboardView({ metrics, data, setModalType, setActiveTab }) {
  return (
    <div className="space-y-6">
      {/* Metric Cards Overview */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
        <MetricCard 
          title='住讱 讛讻诇 讬转专讛 讘讞砖讘讜谞讜转' 
          amount={metrics.totalBalance} 
          icon={<Wallet className="text-emerald-800" />}
          bgColor="bg-emerald-50"
          borderColor="border-emerald-200"
          subText="诪讝讜诪谉 + 注讜状砖 + 讗专谞拽讬诐"
        />
        <MetricCard 
          title="讛讻谞住讜转 讛讞讜讚砖" 
          amount={metrics.currentMonthIncome} 
          icon={<ArrowUpRight className="text-teal-700" />}
          bgColor="bg-teal-50"
          borderColor="border-teal-200"
          trend="up"
        />
        <MetricCard 
          title="讛讜爪讗讜转 讛讞讜讚砖" 
          amount={metrics.currentMonthExpense} 
          icon={<ArrowDownRight className="text-rose-700" />}
          bgColor="bg-rose-50"
          borderColor="border-rose-200"
          trend="down"
        />
        <MetricCard 
          title="讻住祝 驻谞讜讬 诪砖讜注专" 
          amount={metrics.freeCapital} 
          icon={<Sparkles className="text-amber-700" />}
          bgColor="bg-amber-50"
          borderColor="border-amber-200"
          subText="诇讗讞专 讛转讞砖讘讜转 讘讞讜讘讜转 讜讛转讞讬讬讘讜讬讜转"
        />
      </div>

      {/* Loans & Debts Summary Banner */}
      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div className="bg-white p-5 rounded-2xl border border-slate-100 shadow-sm flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="p-3 bg-emerald-100/60 rounded-xl text-emerald-800">
              <HandCoins className="w-6 h-6" />
            </div>
            <div>
              <p className="text-xs text-slate-500 font-medium">讻住祝 砖讛诇讜讜讬转 诇讗讞专讬诐</p>
              <h3 className="text-lg font-bold text-slate-800">鈧獅metrics.moneyLent.toLocaleString()}</h3>
            </div>
          </div>
          <button onClick={() => setActiveTab('loans')} className="text-xs font-semibold text-emerald-800 hover:underline">
            诇驻专讟讬诐 &larr;
          </button>
        </div>

        <div className="bg-white p-5 rounded-2xl border border-slate-100 shadow-sm flex items-center justify-between">
          <div className="flex items-center gap-3">
            <div className="p-3 bg-rose-100/60 rounded-xl text-rose-700">
              <AlertCircle className="w-6 h-6" />
            </div>
            <div>
              <p className="text-xs text-slate-500 font-medium">讞讜讘讜转 讜讛诇讜讜讗讜转 砖诇拽讞转</p>
              <h3 className="text-lg font-bold text-slate-800">鈧獅metrics.moneyOwed.toLocaleString()}</h3>
            </div>
          </div>
          <button onClick={() => setActiveTab('loans')} className="text-xs font-semibold text-rose-700 hover:underline">
            诇驻专讟讬诐 &larr;
          </button>
        </div>
      </div>

      {/* Charts & Breakdown Section */}
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
        {/* Recent Transactions List */}
        <div className="lg:col-span-2 bg-white p-6 rounded-2xl border border-slate-100 shadow-sm">
          <div className="flex items-center justify-between mb-4">
            <h3 className="text-base font-bold text-slate-800">驻注讜诇讜转 讗讞专讜谞讜转</h3>
            <button onClick={() => setActiveTab('transactions')} className="text-xs font-semibold text-emerald-800 hover:underline">
              爪驻讛 讘讛讻诇
            </button>
          </div>
          <div className="space-y-3">
            {data.transactions.slice(0, 5).map((tx) => (
              <div key={tx.id} className="flex items-center justify-between p-3 rounded-xl hover:bg-slate-50 transition-colors border border-slate-50">
                <div className="flex items-center gap-3">
                  <div className={`p-2 rounded-xl ${tx.type === 'income' ? 'bg-emerald-100 text-emerald-800' : 'bg-rose-100 text-rose-700'}`}>
                    {tx.type === 'income' ? <ArrowUpRight className="w-4 h-4" /> : <ArrowDownRight className="w-4 h-4" />}
                  </div>
                  <div>
                    <p className="text-sm font-semibold text-slate-800">{tx.title}</p>
                    <p className="text-xs text-slate-400">{tx.category} 鈥� {tx.date}</p>
                  </div>
                </div>
                <span className={`text-sm font-bold ${tx.type === 'income' ? 'text-emerald-700' : 'text-slate-800'}`}>
                  {tx.type === 'income' ? '+' : '-'}鈧獅Number(tx.amount).toLocaleString()}
                </span>
              </div>
            ))}
          </div>
        </div>

        {/* Budget Allocation Progress */}
        <div className="bg-white p-6 rounded-2xl border border-slate-100 shadow-sm">
          <h3 className="text-base font-bold text-slate-800 mb-4">谞讬爪讜诇 转拽爪讬讘讬诐 讞讜讚砖讬</h3>
          <div className="space-y-4">
            {data.categories.slice(0, 4).map((cat) => {
              const spent = data.transactions
                .filter(t => t.category === cat.name && t.type === 'expense')
                .reduce((a, b) => a + Number(b.amount), 0);
              const percent = Math.min(Math.round((spent / cat.budget) * 100), 100);

              return (
                <div key={cat.name} className="space-y-1">
                  <div className="flex justify-between text-xs font-semibold">
                    <span className="text-slate-700">{cat.name}</span>
                    <span className="text-slate-500">鈧獅spent} / 鈧獅cat.budget}</span>
                  </div>
                  <div className="w-full bg-slate-100 h-2 rounded-full overflow-hidden">
                    <div 
                      className={`h-full rounded-full transition-all duration-500 ${
                        percent > 90 ? 'bg-rose-500' : percent > 75 ? 'bg-amber-500' : 'bg-emerald-700'
                      }`}
                      style={{ width: `${percent}%` }}
                    />
                  </div>
                </div>
              );
            })}
          </div>
        </div>
      </div>
    </div>
  );
}

function MetricCard({ title, amount, icon, bgColor, borderColor, subText, trend }) {
  return (
    <div className={`p-5 rounded-2xl border ${borderColor} ${bgColor} shadow-sm transition-all hover:shadow-md`}>
      <div className="flex items-center justify-between mb-2">
        <span className="text-xs font-semibold text-slate-600">{title}</span>
        <div className="p-2 rounded-xl bg-white/80 shadow-xs">{icon}</div>
      </div>
      <h2 className="text-2xl font-black text-slate-900 tracking-tight">
        鈧獅Number(amount).toLocaleString()}
      </h2>
      {subText && <p className="text-[11px] text-slate-500 mt-1 font-medium">{subText}</p>}
    </div>
  );
}

function TransactionsView({ data, onDelete, filterCategory, setFilterCategory, searchTerm, setSearchTerm }) {
  const filteredTransactions = data.transactions.filter(t => {
    const matchesCategory = filterCategory === 'all' || t.category === filterCategory;
    const matchesSearch = t.title.includes(searchTerm) || t.category.includes(searchTerm);
    return matchesCategory && matchesSearch;
  });

  return (
    <div className="space-y-6">
      <div className="flex flex-col md:flex-row gap-4 items-center justify-between">
        <div className="relative w-full md:w-72">
          <Search className="w-4 h-4 absolute right-3 top-3 text-slate-400" />
          <input 
            type="text" 
            placeholder="讞讬驻讜砖 转谞讜注讛..." 
            value={searchTerm}
            onChange={(e) => setSearchTerm(e.target.value)}
            className="w-full pr-9 pl-4 py-2 bg-white border border-slate-200 rounded-xl text-sm focus:outline-none focus:border-emerald-800"
          />
        </div>

        <div className="flex gap-2 w-full md:w-auto overflow-x-auto pb-1">
          <button 
            onClick={() => setFilterCategory('all')} 
            className={`px-3 py-1.5 rounded-xl text-xs font-semibold whitespace-nowrap ${filterCategory === 'all' ? 'bg-emerald-800 text-white' : 'bg-white text-slate-600 border'}`}
          >
            讛讻诇
          </button>
          {data.categories.map(c => (
            <button 
              key={c.name}
              onClick={() => setFilterCategory(c.name)} 
              className={`px-3 py-1.5 rounded-xl text-xs font-semibold whitespace-nowrap ${filterCategory === c.name ? 'bg-emerald-800 text-white' : 'bg-white text-slate-600 border'}`}
            >
              {c.name}
            </button>
          ))}
        </div>
      </div>

      <div className="bg-white rounded-2xl border border-slate-100 shadow-sm overflow-hidden">
        <div className="overflow-x-auto">
          <table className="w-full text-right text-sm text-slate-700">
            <thead className="bg-slate-50 text-slate-500 font-semibold border-b border-slate-100">
              <tr>
                <th className="p-4">转讬讗讜专</th>
                <th className="p-4">拽讟讙讜专讬讛</th>
                <th className="p-4">转讗专讬讱</th>
                <th className="p-4">住讻讜诐</th>
                <th className="p-4">驻注讜诇讜转</th>
              </tr>
            </thead>
            <tbody className="divide-y divide-slate-100">
              {filteredTransactions.map((tx) => (
                <tr key={tx.id} className="hover:bg-slate-50 transition-colors">
                  <td className="p-4 font-semibold text-slate-800">{tx.title}</td>
                  <td className="p-4"><span className="px-2.5 py-1 bg-slate-100 rounded-lg text-xs">{tx.category}</span></td>
                  <td className="p-4 text-xs text-slate-500">{tx.date}</td>
                  <td className={`p-4 font-bold ${tx.type === 'income' ? 'text-emerald-700' : 'text-slate-800'}`}>
                    {tx.type === 'income' ? '+' : '-'}鈧獅Number(tx.amount).toLocaleString()}
                  </td>
                  <td className="p-4">
                    <button onClick={() => onDelete(tx.id)} className="text-slate-400 hover:text-rose-600 p-1">
                      <Trash2 className="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );
}

function LoansView({ data, setData, setModalType }) {
  const handleMarkAsPaid = (id) => {
    setData(prev => ({
      ...prev,
      loans: prev.loans.map(l => l.id === id ? { ...l, remainingAmount: 0, status: 'completed' } : l)
    }));
  };

  return (
    <div className="space-y-6">
      <div className="flex justify-between items-center">
        <div>
          <h2 className="text-lg font-bold text-slate-800">谞讬讛讜诇 讛诇讜讜讗讜转 讜讞讜讘讜转</h2>
          <p className="text-xs text-slate-500">诪注拽讘 诪讚讜讬拽 讗讞专 讻住驻讬诐 砖谞讬转谞讜 讗讜 谞诇拽讞讜</p>
        </div>
        <button 
          onClick={() => setModalType('loan')} 
          className="bg-emerald-800 text-white text-xs font-semibold px-3 py-2 rounded-xl flex items-center gap-1.5 shadow-sm"
        >
          <Plus className="w-4 h-4" /> 讛诇讜讜讗讛 讞讚砖讛
        </button>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
        {data.loans.map((loan) => (
          <div key={loan.id} className="bg-white p-5 rounded-2xl border border-slate-100 shadow-sm space-y-3">
            <div className="flex justify-between items-start">
              <div>
                <span className={`text-[10px] font-bold px-2 py-0.5 rounded-md ${loan.type === 'given' ? 'bg-emerald-100 text-emerald-800' : 'bg-rose-100 text-rose-700'}`}>
                  {loan.type === 'given' ? '讛诇讜讜讬转讬 诇' : '诇讜讜讬转讬 诪'}
                </span>
                <h3 className="text-base font-bold text-slate-800 mt-1">{loan.personName}</h3>
              </div>
              <span className={`text-xs font-semibold px-2 py-1 rounded-lg ${loan.status === 'completed' ? 'bg-slate-100 text-slate-600' : 'bg-amber-100 text-amber-800'}`}>
                {loan.status === 'completed' ? '讛讜砖诇诐' : '驻注讬诇'}
              </span>
            </div>

            <div className="flex justify-between items-baseline border-t border-b border-slate-50 py-2">
              <div>
                <p className="text-[11px] text-slate-400">住讻讜诐 诪拽讜专讬</p>
                <p className="text-sm font-semibold text-slate-700">鈧獅loan.principalAmount.toLocaleString()}</p>
              </div>
              <div className="text-left">
                <p className="text-[11px] text-slate-400">讬转专讛 诇驻讬专注讜谉</p>
                <p className="text-lg font-bold text-slate-900">鈧獅loan.remainingAmount.toLocaleString()}</p>
              </div>
            </div>

            {loan.notes && <p className="text-xs text-slate-500 italic">"{loan.notes}"</p>}

            {loan.status === 'active' && (
              <button 
                onClick={() => handleMarkAsPaid(loan.id)}
                className="w-full py-2 bg-slate-50 hover:bg-emerald-50 hover:text-emerald-800 text-slate-600 text-xs font-semibold rounded-xl transition-all"
              >
                住诪谉 讻住讜诇拽 讘诪诇讜讗讜
              </button>
            )}
          </div>
        ))}
      </div>
