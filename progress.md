import { useState, useRef, useCallback } from 'react';
import { motion, AnimatePresence } from 'framer-motion';
import * as Icons from 'lucide-react';

const NUTRITION_META = [
  { key: 'protein', label: 'Protein', unit: 'g', icon: 'Dumbbell', color: 'from-blue-500/20 to-blue-600/10', border: 'border-blue-500/20', text: 'text-blue-300' },
  { key: 'carbohydrates', label: 'Carbohydrates', unit: 'g', icon: 'Wheat', color: 'from-amber-500/20 to-amber-600/10', border: 'border-amber-500/20', text: 'text-amber-300' },
  { key: 'fiber', label: 'Fiber', unit: 'g', icon: 'Leaf', color: 'from-emerald-500/20 to-emerald-600/10', border: 'border-emerald-500/20', text: 'text-emerald-300' },
  { key: 'glucose', label: 'Glucose Index', unit: 'GI', icon: 'Activity', color: 'from-rose-500/20 to-rose-600/10', border: 'border-rose-500/20', text: 'text-rose-300' },
];

export default function FoodScanSection({ onScan, nutritionData }) {
  const [isDragging, setIsDragging] = useState(false);
  const [previewUrl, setPreviewUrl] = useState(null);
  const [fileName, setFileName] = useState(null);
  const [isScanning, setIsScanning] = useState(false);
  const [scanComplete, setScanComplete] = useState(false);
  const [error, setError] = useState(null);
  const fileInputRef = useRef(null);

  const handleFile = useCallback((file) => {
    if (!file) return;
    const allowed = ['image/jpeg', 'image/png', 'image/webp', 'image/gif'];
    if (!allowed.includes(file.type)) {
      setError('Please upload a valid image file (JPG, PNG, WEBP).');
      return;
    }
    if (file.size > 10 * 1024 * 1024) {
      setError('File size must be under 10MB.');
      return;
    }
    setError(null);
    setScanComplete(false);
    setFileName(file.name);
    const reader = new FileReader();
    reader.onload = (e) => setPreviewUrl(e.target.result);
    reader.readAsDataURL(file);
  }, []);

  const handleFileChange = (e) => {
    const file = e.target.files?.[0];
    if (file) handleFile(file);
  };

  const handleDrop = (e) => {
    e.preventDefault();
    setIsDragging(false);
    const file = e.dataTransfer.files?.[0];
    if (file) handleFile(file);
  };

  const handleDragOver = (e) => {
    e.preventDefault();
    setIsDragging(true);
  };

  const handleDragLeave = () => setIsDragging(false);

  const handleScan = async () => {
    if (!previewUrl) {
      setError('Please upload a food image first.');
      return;
    }
    setError(null);
    setIsScanning(true);
    setScanComplete(false);
    await new Promise((res) => setTimeout(res, 2200));
    setIsScanning(false);
    setScanComplete(true);
    if (onScan) onScan(previewUrl);
  };

  const handleClear = () => {
    setPreviewUrl(null);
    setFileName(null);
    setScanComplete(false);
    setError(null);
    if (fileInputRef.current) fileInputRef.current.value = '';
  };

  const hasResults = nutritionData && scanComplete;

  const ScanIcon = Icons?.['ScanLine'] || Icons?.['Search'] || Icons.HelpCircle;
  const UploadIcon = Icons?.['UploadCloud'] || Icons?.['Upload'] || Icons.HelpCircle;
  const CheckIcon = Icons?.['CheckCircle2'] || Icons?.['Check'] || Icons.HelpCircle;
  const XIcon = Icons?.['X'] || Icons.HelpCircle;
  const AlertIcon = Icons?.['AlertCircle'] || Icons.HelpCircle;
  const LoaderIcon = Icons?.['Loader2'] || Icons.HelpCircle;
  const ImageIcon = Icons?.['ImagePlus'] || Icons.HelpCircle;

  return (
    <motion.section
      initial={{ opacity: 0, y: 32 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 0.7, ease: [0.22, 1, 0.36, 1] }}
      className="w-full max-w-screen-xl mx-auto px-4 sm:px-8 py-12"
    >
      <div className="flex flex-col lg:flex-row gap-8 items-stretch">

        <motion.div
          initial={{ opacity: 0, x: -24 }}
          animate={{ opacity: 1, x: 0 }}
          transition={{ duration: 0.65, delay: 0.1, ease: [0.22, 1, 0.36, 1] }}
          className="flex-1 min-w-0"
        >
          <div className="h-full rounded-2xl bg-white/5 backdrop-blur-xl border border-white/10 shadow-2xl shadow-black/30 p-8 flex flex-col gap-6">

            <div className="flex items-center gap-3">
              <div className="flex items-center justify-center w-10 h-10 rounded-xl bg-indigo-500/20 border border-indigo-400/20">
                <ScanIcon className="w-5 h-5 text-indigo-300" strokeWidth={1.8} />
              </div>
              <div>
                <h2 className="font-inter text-lg font-semibold text-white tracking-tight leading-none">Food Scanner</h2>
                <p className="font-inter text-xs text-white/40 mt-0.5">Upload a food photo for instant analysis</p>
              </div>
            </div>

            <AnimatePresence mode="wait">
              {previewUrl ? (
                <motion.div
                  key="preview"
                  initial={{ opacity: 0, scale: 0.96 }}
                  animate={{ opacity: 1, scale: 1 }}
                  exit={{ opacity: 0, scale: 0.94 }}
                  transition={{ duration: 0.4 }}
                  className="relative rounded-2xl overflow-hidden border border-white/10 bg-black/20"
                >
                  <img
                    src={previewUrl}
                    alt="Food preview"
                    className="w-full h-52 object-cover"
                    onError={(e) => { e.target.src = 'https://placehold.co/600x300/1e2040/7c8db5?text=Preview'; }}
                  />
                  <button
                    onClick={handleClear}
                    aria-label="Remove image"
                    className="absolute top-3 right-3 flex items-center justify-center w-8 h-8 rounded-full bg-black/50 border border-white/20 text-white/70 hover:text-white hover:bg-black/70 transition-all duration-200"
                  >
                    <XIcon className="w-4 h-4" strokeWidth={2} />
                  </button>
                  {scanComplete && (
                    <div className="absolute top-3 left-3 flex items-center gap-1.5 px-2.5 py-1 rounded-full bg-emerald-500/20 border border-emerald-400/30 backdrop-blur-sm">
                      <CheckIcon className="w-3.5 h-3.5 text-emerald-400" strokeWidth={2} />
                      <span className="font-inter text-xs font-medium text-emerald-300">Scanned</span>
                    </div>
                  )}
                  {fileName && (
                    <div className="px-4 py-2.5 border-t border-white/8">
                      <p className="font-inter text-xs text-white/40 truncate">{fileName}</p>
                    </div>
                  )}
                </motion.div>
              ) : (
                <motion.div
                  key="dropzone"
                  initial={{ opacity: 0 }}
                  animate={{ opacity: 1 }}
                  exit={{ opacity: 0 }}
                  transition={{ duration: 0.3 }}
                  onClick={() => fileInputRef.current?.click()}
                  onDrop={handleDrop}
                  onDragOver={handleDragOver}
                  onDragLeave={handleDragLeave}
                  role="button"
                  tabIndex={0}
                  aria-label="Upload food image dropzone"
                  onKeyDown={(e) => e.key === 'Enter' && fileInputRef.current?.click()}
                  className={`cursor-pointer rounded-2xl border-2 border-dashed transition-all duration-300 flex flex-col items-center justify-center gap-4 py-12 px-6 ${
                    isDragging
                      ? 'border-indigo-400/70 bg-indigo-500/10 scale-[1.01]'
                      : 'border-white/15 bg-white/3 hover:border-indigo-400/40 hover:bg-indigo-500/5'
                  }`}
                >
                  <div className={`flex items-center justify-center w-14 h-14 rounded-2xl border transition-all duration-300 ${
                    isDragging ? 'bg-indigo-500/25 border-indigo-400/40' : 'bg-white/5 border-white/10'
                  }`}>
                    <UploadIcon className={`w-6 h-6 transition-colors duration-300 ${
                      isDragging ? 'text-indigo-300' : 'text-white/40'
                    }`} strokeWidth={1.6} />
                  </div>
                  <div className="text-center">
                    <p className="font-inter text-sm font-medium text-white/70">
                      {isDragging ? 'Release to upload' : 'Drop food image here'}
                    </p>
                    <p className="font-inter text-xs text-white/30 mt-1">or click to browse &bull; JPG, PNG, WEBP &bull; max 10MB</p>
                  </div>
                </motion.div>
              )}
            </AnimatePresence>

            <input
              ref={fileInputRef}
              type="file"
              accept="image/jpeg,image/png,image/webp,image/gif"
              className="hidden"
              onChange={handleFileChange}
              aria-label="File upload input"
            />

            <AnimatePresence>
              {error && (
                <motion.div
                  initial={{ opacity: 0, y: -8 }}
                  animate={{ opacity: 1, y: 0 }}
                  exit={{ opacity: 0, y: -8 }}
                  transition={{ duration: 0.25 }}
                  className="flex items-center gap-2.5 px-4 py-3 rounded-xl bg-rose-500/10 border border-rose-500/20"
                >
                  <AlertIcon className="w-4 h-4 text-rose-400 shrink-0" strokeWidth={1.8} />
                  <p className="font-inter text-xs text-rose-300">{error}</p>
                </motion.div>
              )}
            </AnimatePresence>

            <button
              onClick={handleScan}
              disabled={isScanning || !previewUrl}
              aria-label="Scan food image"
              className={`relative w-full py-4 px-8 rounded-full font-inter text-sm font-semibold tracking-wide transition-all duration-300 overflow-hidden ${
                isScanning || !previewUrl
                  ? 'bg-white/5 border border-white/10 text-white/25 cursor-not-allowed'
                  : 'bg-gradient-to-r from-indigo-600 to-indigo-500 text-white shadow-lg shadow-indigo-500/30 hover:shadow-indigo-500/50 hover:from-indigo-500 hover:to-indigo-400 hover:scale-[1.02] active:scale-[0.99]'
              }`}
            >
              <span className="relative z-10 flex items-center justify-center gap-2.5">
                {isScanning ? (
                  <>
                    <LoaderIcon className="w-4 h-4 animate-spin" strokeWidth={2} />
                    Analyzing Nutrition...
                  </>
                ) : scanComplete ? (
                  <>
                    <CheckIcon className="w-4 h-4" strokeWidth={2} />
                    Scan Again
                  </>
                ) : (
                  <>
                    <ScanIcon className="w-4 h-4" strokeWidth={2} />
                    Scan Food Image
                  </>
                )}
              </span>
            </button>

          </div>
        </motion.div>

        <motion.div
          initial={{ opacity: 0, x: 24 }}
          animate={{ opacity: 1, x: 0 }}
          transition={{ duration: 0.65, delay: 0.2, ease: [0.22, 1, 0.36, 1] }}
          className="flex-1 min-w-0"
        >
          <div className="h-full rounded-2xl bg-white/5 backdrop-blur-xl border border-white/10 shadow-2xl shadow-black/30 p-8 flex flex-col gap-6">

            <div className="flex items-center gap-3">
              <div className="flex items-center justify-center w-10 h-10 rounded-xl bg-emerald-500/20 border border-emerald-400/20">
                <Icons.BarChart3 className="w-5 h-5 text-emerald-300" strokeWidth={1.8} />
              </div>
              <div>
                <h2 className="font-inter text-lg font-semibold text-white tracking-tight leading-none">Nutritional Breakdown</h2>
                <p className="font-inter text-xs text-white/40 mt-0.5">Per serving analysis</p>
              </div>
            </div>

            <AnimatePresence mode="wait">
              {!hasResults ? (
                <motion.div
                  key="empty"
                  initial={{ opacity: 0 }}
                  animate={{ opacity: 1 }}
                  exit={{ opacity: 0 }}
                  transition={{ duration: 0.35 }}
                  className="flex-1 flex flex-col items-center justify-center gap-4 py-10"
                >
                  <div className="flex items-center justify-center w-16 h-16 rounded-2xl bg-white/5 border border-white/8">
                    <ImageIcon className="w-7 h-7 text-white/20" strokeWidth={1.4} />
                  </div>
                  <div className="text-center">
                    <p className="font-inter text-sm font-medium text-white/30">No data yet</p>
                    <p className="font-inter text-xs text-white/18 mt-1">Upload and scan a food image to see its nutritional profile</p>
                  </div>
                  <div className="grid grid-cols-2 gap-3 w-full mt-2">
                    {NUTRITION_META.map((meta, i) => (
                      <div
                        key={meta.key}
                        className="rounded-2xl border border-white/6 bg-white/3 p-5 flex flex-col gap-2"
                      >
                        <div className="w-6 h-1.5 rounded-full bg-white/10" />
                        <div className="w-10 h-4 rounded-md bg-white/8" />
                        <div className="w-14 h-2.5 rounded-sm bg-white/5" />
                      </div>
                    ))}
                  </div>
                </motion.div>
              ) : (
                <motion.div
                  key="results"
                  initial={{ opacity: 0, y: 12 }}
                  animate={{ opacity: 1, y: 0 }}
                  transition={{ duration: 0.5, ease: [0.22, 1, 0.36, 1] }}
                  className="flex-1 flex flex-col gap-4"
                >
                  {nutritionData?.foodName && (
                    <div className="flex items-center gap-2 px-4 py-2.5 rounded-xl bg-white/5 border border-white/10">
                      <Icons.Utensils className="w-4 h-4 text-indigo-300 shrink-0" strokeWidth={1.8} />
                      <span className="font-inter text-sm font-semibold text-white/80 truncate">{nutritionData.foodName}</span>
                      {nutritionData?.calories && (
                        <span className="ml-auto font-inter text-xs font-medium text-white/40 shrink-0">{nutritionData.calories} kcal</span>
                      )}
                    </div>
                  )}
                  <div className="grid grid-cols-2 gap-4">
                    {NUTRITION_META.map((meta, i) => {
                      const NutrIcon = Icons?.[meta.icon] || Icons.HelpCircle;
                      const value = nutritionData?.[meta.key] ?? '--';
                      return (
                        <motion.div
                          key={meta.key}
                          initial={{ opacity: 0, y: 16, scale: 0.96 }}
                          animate={{ opacity: 1, y: 0, scale: 1 }}
                          transition={{ duration: 0.45, delay: i * 0.08, ease: [0.22, 1, 0.36, 1] }}
                          className={`rounded-2xl border ${meta.border} bg-gradient-to-br ${meta.color} p-5 flex flex-col gap-3 group hover:scale-[1.02] transition-transform duration-300`}
                        >
                          <div className="flex items-center justify-between">
                            <NutrIcon className={`w-4 h-4 ${meta.text} opacity-80`} strokeWidth={1.8} />
                            <span className={`font-inter text-xs font-medium ${meta.text} opacity-50 uppercase tracking-widest`}>{meta.unit}</span>
                          </div>
                          <div>
                            <p className="font-inter text-2xl font-bold text-white tracking-tight leading-none">
                              {value}
                              <span className={`text-sm font-medium ${meta.text} ml-1 opacity-70`}>{meta.unit}</span>
                            </p>
                            <p className="font-inter text-xs font-semibold text-white/45 mt-1.5 uppercase tracking-wider">{meta.label}</p>
                          </div>
                        </motion.div>
                      );
                    })}
                  </div>
                  {nutritionData?.note && (
                    <motion.div
                      initial={{ opacity: 0 }}
                      animate={{ opacity: 1 }}
                      transition={{ delay: 0.4 }}
                      className="flex items-start gap-2.5 px-4 py-3 rounded-xl bg-indigo-500/8 border border-indigo-400/15"
                    >
                      <Icons.Info className="w-4 h-4 text-indigo-300 shrink-0 mt-0.5" strokeWidth={1.8} />
                      <p className="font-inter text-xs text-white/50 leading-relaxed">{nutritionData.note}</p>
                    </motion.div>
                  )}
                </motion.div>
              )}
            </AnimatePresence>

          </div>
        </motion.div>

      </div>
    </motion.section>
  );
}
