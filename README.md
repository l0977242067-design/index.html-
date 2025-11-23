<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>网络电话信号转换器</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
        }
        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 10px;
            font-size: 2.5em;
        }
        .subtitle {
            text-align: center;
            color: #666;
            margin-bottom: 30px;
            font-size: 1.2em;
        }
        .control-panel {
            background: #f8f9fa;
            padding: 25px;
            border-radius: 15px;
            margin-bottom: 25px;
        }
        .btn-group {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 12px;
            margin-bottom: 20px;
        }
        button {
            background: #007bff;
            color: white;
            border: none;
            padding: 15px 20px;
            border-radius: 10px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0,123,255,0.3);
        }
        button:hover {
            background: #0056b3;
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0,123,255,0.4);
        }
        button:active {
            transform: translateY(0);
        }
        .btn-primary { background: #007bff; }
        .btn-success { background: #28a745; }
        .btn-warning { background: #ffc107; color: #212529; }
        .btn-danger { background: #dc3545; }
        .btn-info { background: #17a2b8; }
        .status {
            background: #e9ecef;
            padding: 15px;
            border-radius: 10px;
            margin: 15px 0;
            font-weight: 500;
            text-align: center;
        }
        .status.active {
            background: #d4edda;
            color: #155724;
        }
        .status.error {
            background: #f8d7da;
            color: #721c24;
        }
        .wave-display {
            background: #1a1a1a;
            border-radius: 10px;
            padding: 20px;
            margin: 20px 0;
        }
        canvas {
            width: 100%;
            height: 200px;
            border-radius: 5px;
        }
        .custom-controls {
            background: white;
            padding: 20px;
            border-radius: 10px;
            border: 2px solid #e9ecef;
            margin-top: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            color: #495057;
        }
        input[type="number"] {
            width: 100%;
            padding: 12px;
            border: 2px solid #e9ecef;
            border-radius: 8px;
            font-size: 16px;
            transition: border-color 0.3s ease;
        }
        input[type="number"]:focus {
            outline: none;
            border-color: #007bff;
        }
        .signal-info {
            background: #fff3cd;
            padding: 15px;
            border-radius: 10px;
            margin: 15px 0;
            border-left: 4px solid #ffc107;
        }
        .feature-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 15px;
            margin: 25px 0;
        }
        .feature-card {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            border: 1px solid #e9ecef;
        }
        .feature-icon {
            font-size: 2em;
            margin-bottom: 10px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>📞 网络电话信号转换器</h1>
        <div class="subtitle">直接在浏览器中转换网络信号为电话波形 - 无需安装任何软件</div>
        
        <div class="feature-grid">
            <div class="feature-card">
                <div class="feature-icon">⚡</div>
                <h3>实时转换</h3>
                <p>网络信号即时转换为电话波形</p>
            </div>
            <div class="feature-card">
                <div class="feature-icon">🔊</div>
                <h3>音频播放</h3>
                <p>直接在浏览器中播放转换结果</p>
            </div>
            <div class="feature-card">
                <div class="feature-icon">📱</div>
                <h3>移动友好</h3>
                <p>完美支持iOS和Android设备</p>
            </div>
        </div>

        <div class="control-panel">
            <h2>🎛️ 信号控制面板</h2>
            <div class="btn-group">
                <button class="btn-primary" onclick="generateDialTone()">生成拨号音</button>
                <button class="btn-success" onclick="generateRingTone()">生成回铃音</button>
                <button class="btn-warning" onclick="generateBusyTone()">生成忙线音</button>
                <button class="btn-danger" onclick="stopAllAudio()">停止所有音频</button>
            </div>
            
            <div class="status" id="status">就绪：点击上方按钮开始生成电话信号</div>
            
            <div class="signal-info">
                <strong>当前信号：</strong>
                <span id="currentSignal">无</span>
                <br>
                <strong>频率组成：</strong>
                <span id="frequencyInfo">-</span>
            </div>
        </div>

        <div class="wave-display">
            <h3>📊 实时波形显示</h3>
            <canvas id="waveformCanvas"></canvas>
        </div>

        <div class="custom-controls">
            <h3>🔧 自定义信号生成器</h3>
            <div class="form-group">
                <label for="freq1">主频率 (Hz):</label>
                <input type="number" id="freq1" value="350" min="50" max="2000">
            </div>
            <div class="form-group">
                <label for="freq2">次频率 (Hz) - 可选:</label>
                <input type="number" id="freq2" value="440" min="50" max="2000">
            </div>
            <div class="form-group">
                <label for="duration">持续时间 (秒):</label>
                <input type="number" id="duration" value="3" min="0.5" max="10" step="0.5">
            </div>
            <button class="btn-info" onclick="generateCustomTone()">生成自定义音调</button>
        </div>
    </div>

    <script>
        class TelephoneSignalConverter {
            constructor() {
                this.audioContext = null;
                this.oscillators = [];
                this.isPlaying = false;
                this.canvas = document.getElementById('waveformCanvas');
                this.ctx = this.canvas.getContext('2d');
                this.currentSignal = null;
                
                this.initAudioContext();
                this.setupCanvas();
                this.animateWaveform();
            }
            
            initAudioContext() {
                try {
                    this.audioContext = new (window.AudioContext || window.webkitAudioContext)();
                    this.updateStatus('音频系统初始化完成', 'active');
                } catch (e) {
                    this.updateStatus('您的浏览器不支持Web Audio API', 'error');
                }
            }
            
            setupCanvas() {
                this.canvas.width = this.canvas.offsetWidth;
                this.canvas.height = this.canvas.offsetHeight;
            }
            
            updateStatus(message, type = '') {
                const statusEl = document.getElementById('status');
                statusEl.textContent = message;
                statusEl.className = 'status ' + type;
            }
            
            updateSignalInfo(signalName, frequencies) {
                document.getElementById('currentSignal').textContent = signalName;
                document.getElementById('frequencyInfo').textContent = frequencies.join('Hz + ') + 'Hz';
            }
            
            generateTone(frequencies, duration, type = 'continuous') {
                if (!this.audioContext) {
                    this.initAudioContext();
                }
                
                this.stopAllAudio();
                this.oscillators = [];
                this.isPlaying = true;
                
                const gainNode = this.audioContext.createGain();
                gainNode.connect(this.audioContext.destination);
                
                frequencies.forEach(freq => {
                    const oscillator = this.audioContext.createOscillator();
                    oscillator.type = 'sine';
                    oscillator.frequency.value = freq;
                    
                    if (type === 'pulsed') {
                        // 脉冲类型（用于忙线音、回铃音）
                        const pulseGain = this.audioContext.createGain();
                        oscillator.connect(pulseGain);
                        pulseGain.connect(gainNode);
                        
                        const now = this.audioContext.currentTime;
                        pulseGain.gain.setValueAtTime(0.3, now);
                        
                        if (frequencies[0] === 480) { // 忙线音：快速脉冲
                            pulseGain.gain.setValueAtTime(0.3, now);
                            pulseGain.gain.setValueAtTime(0, now + 0.25);
                            pulseGain.gain.setValueAtTime(0.3, now + 0.5);
                            pulseGain.gain.setValueAtTime(0, now + 0.75);
                        } else { // 回铃音：慢速脉冲
                            pulseGain.gain.setValueAtTime(0.3, now);
                            pulseGain.gain.setValueAtTime(0, now + 2.0);
                            pulseGain.gain.setValueAtTime(0.3, now + 4.0);
                        }
                    } else {
                        // 连续类型（用于拨号音）
                        oscillator.connect(gainNode);
                        gainNode.gain.value = 0.2;
                    }
                    
                    oscillator.start();
                    this.oscillators.push(oscillator);
                });
                
                // 设置停止时间
                if (type === 'continuous') {
                    const stopTime = this.audioContext.currentTime + duration;
                    this.oscillators.forEach(osc => osc.stop(stopTime));
                    
                    // 音频停止时更新状态
                    setTimeout(() => {
                        if (this.isPlaying) {
                            this.isPlaying = false;
                            this.updateStatus('音频播放完成');
                        }
                    }, duration * 1000);
                }
            }
            
            generateDialTone() {
                this.generateTone([350, 440], 5, 'continuous');
                this.updateStatus('正在播放拨号音 (350Hz + 440Hz)', 'active');
                this.updateSignalInfo('拨号音', [350, 440]);
                this.currentSignal = 'dial';
            }
            
            generateRingTone() {
                this.generateTone([440], 8, 'pulsed');
                this.updateStatus('正在播放回铃音 (440Hz, 2秒脉冲)', 'active');
                this.updateSignalInfo('回铃音', [440]);
                this.currentSignal = 'ring';
            }
            
            generateBusyTone() {
                this.generateTone([480, 620], 6, 'pulsed');
                this.updateStatus('正在播放忙线音 (480Hz + 620Hz, 0.5秒脉冲)', 'active');
                this.updateSignalInfo('忙线音', [480, 620]);
                this.currentSignal = 'busy';
            }
            
            generateCustomTone() {
                const freq1 = parseFloat(document.getElementById('freq1').value);
                const freq2 = parseFloat(document.getElementById('freq2').value);
                const duration = parseFloat(document.getElementById('duration').value);
                
                const frequencies = freq2 > 0 ? [freq1, freq2] : [freq1];
                this.generateTone(frequencies, duration, 'continuous');
                this.updateStatus(`正在播放自定义音调: ${frequencies.join('Hz + ')}Hz`, 'active');
                this.updateSignalInfo('自定义音调', frequencies);
                this.currentSignal = 'custom';
            }
            
            stopAllAudio() {
                this.oscillators.forEach(oscillator => {
                    try {
                        oscillator.stop();
                        oscillator.disconnect();
                    } catch (e) {
                        // 忽略已停止的oscillator错误
                    }
                });
                this.oscillators = [];
                this.isPlaying = false;
                this.updateStatus('音频已停止');
            }
            
            animateWaveform() {
                requestAnimationFrame(() => this.animateWaveform());
                
                this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
                
                if (this.isPlaying) {
                    this.ctx.strokeStyle = '#007bff';
                    this.ctx.lineWidth = 3;
                    this.ctx.beginPath();
                    
                    const time = Date.now() * 0.005;
                    const amplitude = this.canvas.height / 3;
                    const centerY = this.canvas.height / 2;
                    
                    for (let x = 0; x < this.canvas.width; x++) {
                        const t = (x / this.canvas.width) * Math.PI * 8 + time;
                        let y = centerY;
                        
                        if (this.currentSignal === 'dial') {
                            y += Math.sin(t * 0.7) * amplitude * 0.3 + Math.sin(t * 0.88) * amplitude * 0.3;
                        } else if (this.currentSignal === 'ring') {
                            const pulse = Math.sin(time * 0.5) > 0 ? 1 : 0;
                            y += Math.sin(t) * amplitude * 0.4 * pulse;
                        } else if (this.currentSignal === 'busy') {
                            const pulse = Math.sin(time * 8) > 0 ? 1 : 0;
                            y += Math.sin(t * 1.2) * amplitude * 0.3 * pulse;
                        } else if (this.currentSignal === 'custom') {
                            y += Math.sin(t) * amplitude * 0.5;
                        }
                        
                        if (x === 0) {
                            this.ctx.moveTo(x, y);
                        } else {
                            this.ctx.lineTo(x, y);
                        }
                    }
                    
                    this.ctx.stroke();
                } else {
                    // 显示静默状态
                    this.ctx.strokeStyle = '#6c757d';
                    this.ctx.lineWidth = 1;
                    this.ctx.beginPath();
                    this.ctx.moveTo(0, this.canvas.height / 2);
                    this.ctx.lineTo(this.canvas.width, this.canvas.height / 2);
                    this.ctx.stroke();
                    
                    this.ctx.fillStyle = '#6c757d';
                    this.ctx.font = '16px Arial';
                    this.ctx.textAlign = 'center';
                    this.ctx.fillText('无活动信号 - 点击上方按钮生成波形', this.canvas.width / 2, this.canvas.height / 2);
                }
            }
        }
        
        // 初始化转换器
        let converter;
        window.addEventListener('load', () => {
            converter = new TelephoneSignalConverter();
        });
        
        // 全局函数供按钮调用
        function generateDialTone() {
            converter.generateDialTone();
        }
        
        function generateRingTone() {
            converter.generateRingTone();
        }
        
        function generateBusyTone() {
            converter.generateBusyTone();
        }
        
        function generateCustomTone() {
            converter.generateCustomTone();
        }
        
        function stopAllAudio() {
            converter.stopAllAudio();
        }
        
        // 响应窗口大小变化
        window.addEventListener('resize', () => {
            converter.setupCanvas();
        });
    </script>
</body>
</html>
