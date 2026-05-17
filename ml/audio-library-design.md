# Audio Processing Library - Low Level Design

## Interview Question

**Question: Design an Audio Processing Library**

You are tasked with designing a Python audio processing library with the following requirements:

1. **Format Conversion**: Convert audio between formats (MP3, WAV, FLAC, etc.)
2. **Storage**: Save audio to multiple backends:
   - Cloud bucket (S3/GCS)
   - PostgreSQL (for byte arrays)
   - BigQuery
3. **Serialization**: Convert audio arrays (numpy) to files and vice versa
4. **Audio Cleaning**: Remove noise, normalize, trim silence

Design the library with:
- Class diagrams
- Design patterns used
- Interface definitions
- Example usage

---

## Answer

### Design Patterns Used

| Pattern | Use Case |
|---------|----------|
| **Strategy** | Swappable storage backends |
| **Factory** | Create format-specific converters |
| **Adapter** | Uniform interface for different audio formats |
| **Pipeline/Chain** | Composable audio processing steps |
| **Repository** | Abstract storage operations |

---

### Class Diagram (ASCII)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CORE LAYER                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌──────────────┐          ┌──────────────────┐                            │
│   │  AudioData   │          │  AudioMetadata   │                            │
│   ├──────────────┤          ├──────────────────┤                            │
│   │ - samples    │◄─────────│ - sample_rate    │                            │
│   │ - metadata   │          │ - channels       │                            │
│   │ - format     │          │ - duration       │                            │
│   └──────────────┘          │ - bit_depth      │                            │
│                             └──────────────────┘                            │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           CONVERSION LAYER (Strategy + Factory)             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌────────────────────────┐                                                │
│   │ <<interface>>          │                                                │
│   │ FormatConverter        │                                                │
│   ├────────────────────────┤                                                │
│   │ + encode(AudioData)    │                                                │
│   │ + decode(bytes)        │                                                │
│   └───────────┬────────────┘                                                │
│               │                                                              │
│     ┌─────────┼─────────┬─────────────┐                                     │
│     ▼         ▼         ▼             ▼                                     │
│ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐                                 │
│ │MP3Conv │ │WAVConv │ │FLACConv│ │OGGConv │                                 │
│ └────────┘ └────────┘ └────────┘ └────────┘                                 │
│                                                                              │
│   ┌─────────────────────────┐                                               │
│   │ ConverterFactory        │  ◄── Creates appropriate converter            │
│   ├─────────────────────────┤                                               │
│   │ + get_converter(format) │                                               │
│   └─────────────────────────┘                                               │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           STORAGE LAYER (Strategy + Repository)             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌─────────────────────────┐                                               │
│   │ <<interface>>           │                                               │
│   │ StorageBackend          │                                               │
│   ├─────────────────────────┤                                               │
│   │ + save(key, data)       │                                               │
│   │ + load(key) -> bytes    │                                               │
│   │ + delete(key)           │                                               │
│   │ + exists(key) -> bool   │                                               │
│   └───────────┬─────────────┘                                               │
│               │                                                              │
│     ┌─────────┼─────────────┬──────────────┐                                │
│     ▼         ▼             ▼              ▼                                │
│ ┌─────────┐ ┌───────────┐ ┌────────────┐ ┌─────────────┐                    │
│ │Bucket   │ │Postgres   │ │BigQuery    │ │FileSystem   │                    │
│ │Storage  │ │Storage    │ │Storage     │ │Storage      │                    │
│ └─────────┘ └───────────┘ └────────────┘ └─────────────┘                    │
│                                                                              │
│   ┌─────────────────────────┐                                               │
│   │ AudioRepository         │  ◄── High-level storage operations            │
│   ├─────────────────────────┤                                               │
│   │ - backend: StorageBackend                                               │
│   │ + save_audio(AudioData) │                                               │
│   │ + load_audio(key)       │                                               │
│   └─────────────────────────┘                                               │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           PROCESSING LAYER (Pipeline Pattern)               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌─────────────────────────┐                                               │
│   │ <<interface>>           │                                               │
│   │ AudioProcessor          │                                               │
│   ├─────────────────────────┤                                               │
│   │ + process(AudioData)    │                                               │
│   │   -> AudioData          │                                               │
│   └───────────┬─────────────┘                                               │
│               │                                                              │
│     ┌─────────┼─────────┬─────────────┬────────────┐                        │
│     ▼         ▼         ▼             ▼            ▼                        │
│ ┌─────────┐ ┌────────┐ ┌───────────┐ ┌──────────┐ ┌──────────┐              │
│ │Noise    │ │Silence │ │Normalizer │ │Compressor│ │Equalizer │              │
│ │Remover  │ │Trimmer │ │           │ │          │ │          │              │
│ └─────────┘ └────────┘ └───────────┘ └──────────┘ └──────────┘              │
│                                                                              │
│   ┌─────────────────────────────────────────┐                               │
│   │ ProcessingPipeline                      │                               │
│   ├─────────────────────────────────────────┤                               │
│   │ - processors: List[AudioProcessor]      │                               │
│   │ + add(processor)                        │                               │
│   │ + execute(AudioData) -> AudioData       │                               │
│   └─────────────────────────────────────────┘                               │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           SERIALIZATION LAYER                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌─────────────────────────────────────────┐                               │
│   │ AudioSerializer                         │                               │
│   ├─────────────────────────────────────────┤                               │
│   │ + array_to_file(ndarray, path, format)  │                               │
│   │ + file_to_array(path) -> ndarray        │                               │
│   │ + bytes_to_array(bytes) -> ndarray      │                               │
│   │ + array_to_bytes(ndarray, format)       │                               │
│   └─────────────────────────────────────────┘                               │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           FACADE (Main Entry Point)                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌─────────────────────────────────────────┐                               │
│   │ AudioLibrary                            │  ◄── Single entry point       │
│   ├─────────────────────────────────────────┤                               │
│   │ - repository: AudioRepository           │                               │
│   │ - pipeline: ProcessingPipeline          │                               │
│   │ - serializer: AudioSerializer           │                               │
│   │ - converter_factory: ConverterFactory   │                               │
│   ├─────────────────────────────────────────┤                               │
│   │ + load(path_or_key) -> AudioData        │                               │
│   │ + save(audio, destination)              │                               │
│   │ + convert(audio, target_format)         │                               │
│   │ + process(audio, processors)            │                               │
│   │ + clean(audio) -> AudioData             │                               │
│   └─────────────────────────────────────────┘                               │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### Interface Definitions

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import List, Optional
from enum import Enum
import numpy as np

# ============== ENUMS ==============

class AudioFormat(Enum):
    MP3 = "mp3"
    WAV = "wav"
    FLAC = "flac"
    OGG = "ogg"

class StorageType(Enum):
    BUCKET = "bucket"
    POSTGRES = "postgres"
    BIGQUERY = "bigquery"
    FILESYSTEM = "filesystem"

# ============== CORE ==============

@dataclass
class AudioMetadata:
    sample_rate: int
    channels: int
    duration: float
    bit_depth: int
    format: AudioFormat

@dataclass
class AudioData:
    samples: np.ndarray  # Shape: (channels, samples)
    metadata: AudioMetadata
    
    @property
    def duration(self) -> float:
        return len(self.samples[0]) / self.metadata.sample_rate

# ============== CONVERSION (Strategy) ==============

class FormatConverter(ABC):
    @abstractmethod
    def encode(self, audio: AudioData) -> bytes:
        """Convert AudioData to encoded bytes"""
        pass
    
    @abstractmethod
    def decode(self, data: bytes) -> AudioData:
        """Convert encoded bytes to AudioData"""
        pass

class ConverterFactory:
    _converters: dict[AudioFormat, type[FormatConverter]] = {}
    
    @classmethod
    def register(cls, format: AudioFormat, converter: type[FormatConverter]):
        cls._converters[format] = converter
    
    @classmethod
    def get_converter(cls, format: AudioFormat) -> FormatConverter:
        if format not in cls._converters:
            raise ValueError(f"No converter for format: {format}")
        return cls._converters[format]()

# ============== STORAGE (Strategy + Repository) ==============

class StorageBackend(ABC):
    @abstractmethod
    def save(self, key: str, data: bytes) -> None:
        pass
    
    @abstractmethod
    def load(self, key: str) -> bytes:
        pass
    
    @abstractmethod
    def delete(self, key: str) -> None:
        pass
    
    @abstractmethod
    def exists(self, key: str) -> bool:
        pass

class AudioRepository:
    def __init__(self, backend: StorageBackend, converter: FormatConverter):
        self._backend = backend
        self._converter = converter
    
    def save_audio(self, key: str, audio: AudioData) -> None:
        encoded = self._converter.encode(audio)
        self._backend.save(key, encoded)
    
    def load_audio(self, key: str) -> AudioData:
        data = self._backend.load(key)
        return self._converter.decode(data)

# ============== PROCESSING (Pipeline) ==============

class AudioProcessor(ABC):
    @abstractmethod
    def process(self, audio: AudioData) -> AudioData:
        pass

class ProcessingPipeline:
    def __init__(self):
        self._processors: List[AudioProcessor] = []
    
    def add(self, processor: AudioProcessor) -> 'ProcessingPipeline':
        self._processors.append(processor)
        return self  # Fluent interface
    
    def execute(self, audio: AudioData) -> AudioData:
        result = audio
        for processor in self._processors:
            result = processor.process(result)
        return result

# ============== SERIALIZATION ==============

class AudioSerializer:
    def __init__(self, factory: ConverterFactory):
        self._factory = factory
    
    def array_to_file(self, samples: np.ndarray, path: str, 
                      format: AudioFormat, sample_rate: int) -> None:
        pass
    
    def file_to_array(self, path: str) -> tuple[np.ndarray, AudioMetadata]:
        pass
    
    def bytes_to_array(self, data: bytes, format: AudioFormat) -> np.ndarray:
        pass
    
    def array_to_bytes(self, samples: np.ndarray, 
                       format: AudioFormat, sample_rate: int) -> bytes:
        pass
```

---

### Concrete Implementations (Examples)

```python
# ============== FORMAT CONVERTERS ==============

class WAVConverter(FormatConverter):
    def encode(self, audio: AudioData) -> bytes:
        # Use scipy.io.wavfile or soundfile
        import io
        import soundfile as sf
        buffer = io.BytesIO()
        sf.write(buffer, audio.samples.T, audio.metadata.sample_rate, format='WAV')
        return buffer.getvalue()
    
    def decode(self, data: bytes) -> AudioData:
        import io
        import soundfile as sf
        buffer = io.BytesIO(data)
        samples, sample_rate = sf.read(buffer)
        # ... build AudioData

class MP3Converter(FormatConverter):
    def encode(self, audio: AudioData) -> bytes:
        # Use pydub or ffmpeg wrapper
        pass
    
    def decode(self, data: bytes) -> AudioData:
        pass

# Register converters
ConverterFactory.register(AudioFormat.WAV, WAVConverter)
ConverterFactory.register(AudioFormat.MP3, MP3Converter)

# ============== STORAGE BACKENDS ==============

class BucketStorage(StorageBackend):
    def __init__(self, bucket_name: str, credentials: dict):
        self._bucket_name = bucket_name
        # Initialize GCS/S3 client
        
    def save(self, key: str, data: bytes) -> None:
        # self._client.upload_blob(key, data)
        pass
    
    def load(self, key: str) -> bytes:
        # return self._client.download_blob(key)
        pass

class PostgresStorage(StorageBackend):
    """Stores audio as bytea in Postgres - good for small files"""
    
    def __init__(self, connection_string: str):
        self._conn_string = connection_string
    
    def save(self, key: str, data: bytes) -> None:
        # INSERT INTO audio_files (key, data) VALUES (%s, %s)
        pass
    
    def load(self, key: str) -> bytes:
        # SELECT data FROM audio_files WHERE key = %s
        pass

class BigQueryStorage(StorageBackend):
    """Stores audio metadata + bytes in BigQuery"""
    
    def __init__(self, project: str, dataset: str, table: str):
        self._project = project
        self._dataset = dataset
        self._table = table
    
    def save(self, key: str, data: bytes) -> None:
        # Encode as base64, insert into BQ
        pass

# ============== AUDIO PROCESSORS ==============

class NoiseRemover(AudioProcessor):
    def __init__(self, noise_threshold: float = 0.1):
        self._threshold = noise_threshold
    
    def process(self, audio: AudioData) -> AudioData:
        # Use noisereduce library or spectral gating
        import noisereduce as nr
        cleaned = nr.reduce_noise(
            y=audio.samples, 
            sr=audio.metadata.sample_rate
        )
        return AudioData(samples=cleaned, metadata=audio.metadata)

class SilenceTrimmer(AudioProcessor):
    def __init__(self, threshold_db: float = -40):
        self._threshold = threshold_db
    
    def process(self, audio: AudioData) -> AudioData:
        # Trim leading/trailing silence
        pass

class Normalizer(AudioProcessor):
    def __init__(self, target_db: float = -3.0):
        self._target = target_db
    
    def process(self, audio: AudioData) -> AudioData:
        # Peak normalize to target dB
        peak = np.max(np.abs(audio.samples))
        target_linear = 10 ** (self._target / 20)
        normalized = audio.samples * (target_linear / peak)
        return AudioData(samples=normalized, metadata=audio.metadata)
```

---

### Facade - Main Entry Point

```python
class AudioLibrary:
    """Main facade for all audio operations"""
    
    def __init__(self, storage_backend: StorageBackend):
        self._converter_factory = ConverterFactory
        self._serializer = AudioSerializer(self._converter_factory)
        self._storage = storage_backend
    
    # -------- Loading --------
    def load_file(self, path: str) -> AudioData:
        """Load audio from local file"""
        samples, metadata = self._serializer.file_to_array(path)
        return AudioData(samples=samples, metadata=metadata)
    
    def load_from_storage(self, key: str, format: AudioFormat) -> AudioData:
        """Load audio from configured storage backend"""
        converter = self._converter_factory.get_converter(format)
        data = self._storage.load(key)
        return converter.decode(data)
    
    # -------- Saving --------
    def save_file(self, audio: AudioData, path: str, format: AudioFormat) -> None:
        """Save audio to local file"""
        self._serializer.array_to_file(
            audio.samples, path, format, audio.metadata.sample_rate
        )
    
    def save_to_storage(self, audio: AudioData, key: str, format: AudioFormat) -> None:
        """Save audio to configured storage backend"""
        converter = self._converter_factory.get_converter(format)
        encoded = converter.encode(audio)
        self._storage.save(key, encoded)
    
    # -------- Conversion --------
    def convert(self, audio: AudioData, target_format: AudioFormat) -> bytes:
        """Convert audio to target format"""
        converter = self._converter_factory.get_converter(target_format)
        return converter.encode(audio)
    
    # -------- Processing --------
    def clean(self, audio: AudioData, 
              remove_noise: bool = True,
              trim_silence: bool = True,
              normalize: bool = True) -> AudioData:
        """Apply standard cleaning pipeline"""
        pipeline = ProcessingPipeline()
        
        if remove_noise:
            pipeline.add(NoiseRemover())
        if trim_silence:
            pipeline.add(SilenceTrimmer())
        if normalize:
            pipeline.add(Normalizer())
        
        return pipeline.execute(audio)
    
    def process(self, audio: AudioData, 
                processors: List[AudioProcessor]) -> AudioData:
        """Apply custom processing pipeline"""
        pipeline = ProcessingPipeline()
        for p in processors:
            pipeline.add(p)
        return pipeline.execute(audio)
```

---

### Example Usage

```python
# ============== BASIC USAGE ==============

# Initialize with bucket storage
storage = BucketStorage(bucket_name="my-audio-bucket", credentials={...})
audio_lib = AudioLibrary(storage_backend=storage)

# Load from file
audio = audio_lib.load_file("/path/to/recording.wav")

# Clean it
cleaned = audio_lib.clean(audio, remove_noise=True, normalize=True)

# Convert to MP3 and save to cloud
audio_lib.save_to_storage(cleaned, key="cleaned/recording.mp3", format=AudioFormat.MP3)


# ============== CUSTOM PIPELINE ==============

# Create custom processing chain
pipeline = (ProcessingPipeline()
    .add(NoiseRemover(noise_threshold=0.05))
    .add(SilenceTrimmer(threshold_db=-50))
    .add(Normalizer(target_db=-1.0))
)

processed = pipeline.execute(audio)


# ============== SWITCH STORAGE BACKENDS ==============

# Use Postgres for small files
postgres = PostgresStorage(connection_string="postgresql://...")
audio_lib_pg = AudioLibrary(storage_backend=postgres)

# Use BigQuery for analytics
bq = BigQueryStorage(project="my-project", dataset="audio", table="recordings")
audio_lib_bq = AudioLibrary(storage_backend=bq)


# ============== ARRAY <-> FILE CONVERSION ==============

serializer = AudioSerializer(ConverterFactory)

# Numpy array from file
samples, metadata = serializer.file_to_array("/path/to/audio.wav")

# Save array as file
serializer.array_to_file(
    samples=np.random.randn(2, 44100),  # 1 second stereo
    path="/path/to/output.flac",
    format=AudioFormat.FLAC,
    sample_rate=44100
)
```

---

### Design Decisions & Trade-offs

| Decision | Rationale |
|----------|-----------|
| **Strategy for storage** | Easy to add new backends (Redis, S3, etc.) without touching core logic |
| **Factory for converters** | Centralized converter management, easy registration of new formats |
| **Pipeline for processing** | Composable, reorderable, testable processing steps |
| **Facade for API** | Single entry point reduces cognitive load for library users |
| **AudioData as immutable dataclass** | Processors return new instances, safe for concurrent processing |
| **Separate Serializer** | Keeps array/file conversion separate from format encoding/decoding |

### SOLID Principles Applied

- **S**ingle Responsibility: Each class has one job (converter converts, storage stores)
- **O**pen/Closed: Add new formats/processors/storages without modifying existing code
- **L**iskov Substitution: Any StorageBackend can be swapped without breaking functionality
- **I**nterface Segregation: Small, focused interfaces (FormatConverter, StorageBackend, AudioProcessor)
- **D**ependency Inversion: High-level modules depend on abstractions (interfaces), not concrete classes

### Extension Points

1. **New format support**: Implement `FormatConverter`, register with factory
2. **New storage backend**: Implement `StorageBackend`, inject into `AudioLibrary`
3. **New processor**: Implement `AudioProcessor`, add to pipeline
4. **Batch processing**: Extend `AudioLibrary` with async methods using same interfaces
