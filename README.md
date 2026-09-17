# 阿尔比恩桌宠 · TTS 音色权重

桌宠「阿尔比恩」用的本地语音音色包。跑在 [Irodori-TTS](https://github.com/Aratako/Irodori-TTS) 上，
一共五条船：`albion` / `taihou` / `shinano` / `brest` / `jinshi`。

每条船两份东西：

| 文件 | 是什么 | 在哪 |
|---|---|---|
| `<船名>.speaker.safetensors` | 说话人嵌入（约 49 KB） | 本仓库 `speaker/` |
| `<目录名>.safetensors` | LoRA 适配器（约 105 MB） | [Releases](../../releases) 附件 |

大的那份（LoRA）走 Release 附件，不进 git 历史——GitHub 单文件上限 100 MB，塞不进仓库。

## 装法

**0. 前置**：一张能跑推理的 NVIDIA 显卡；上游 Irodori-TTS 已经能出声音。

**1. 装上游**

```bash
git clone https://github.com/Aratako/Irodori-TTS
cd Irodori-TTS
# 按它自己的 README 建环境、下基础模型
```

**2. 把音色放进去**

```bash
# 从本仓库拿 speaker 嵌入
cp -r <本仓库>/speaker/*.safetensors ./voice/speaker/     # 具体目录名以上游为准

# 从 Releases 下载 LoRA，按目录名放好（目录名见下表）
mkdir -p outputs/albion_lora_emo_spk/checkpoint_final
mv albion_lora_emo_spk.safetensors outputs/albion_lora_emo_spk/checkpoint_final/adapter_model.safetensors
# 其余四条同理
```

`lora-meta/<目录名>/` 里是训练留下的配置（`adapter_config.json` / `config.json` / `irodori_lora_metadata.json`），
一并放进同目录即可；`trainer_state.pt` 是训练续跑用的中间状态，**用不上，没随附件发**。

**3. 抄音色表**

`ships.json` 就是「哪条船挂哪个 LoRA、用哪个嵌入目录」的对照表，改完自动生效、不用重启：

```json
"albion": { "lora": "albion_lora_emo_spk", "inv": null }
```

`inv` 留 `null` 表示按 `<船名>_inv` 约定去找嵌入目录。

**4. 起后端**

上游自己的启动方式即可。桌宠那边要的是一个 OpenAI 兼容的 `/v1/audio/speech`，
音色填船名（`voice=albion`），请求里带不带 `response_format` 都会回 wav。

## 五条船

| 船名 | LoRA 目录 |
|---|---|
| `albion` | `albion_lora_emo_spk` |
| `taihou` | `taihou_lora_clean200` |
| `shinano` | `shinano_lora_emo_spk` |
| `brest` | `brest_lora_emo_spk` |
| `jinshi` | `jinshi_lora_clean200` |

## 关于素材与许可

训练数据是从游戏《碧蓝航线》的语音中整理出来的（情绪标注 + 说话人切分），**不随本仓库分发**；
仓库里只有训练产物（LoRA 适配器与说话人嵌入），供本地、个人用途。

角色与语音版权归原权利方；Irodori-TTS 及其基础模型许可见上游仓库。
本仓库的配置文件与训练产物由作者本人制作，沿用 MIT 许可。
