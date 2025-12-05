# Economic Research - Historical Map Analysis

This project extracts and analyzes zoning/planning information from historical newspaper maps using computer vision and AI models.

## Project Structure

```
econ-research/
├── input/                       # Raw input images
│   ├── abilene-reporter-news-apr-29-1946-p-19.png
│   ├── appleton-post-crescent-oct-24-1922-p-13.png
│   └── atchison-daily-globe-nov-30-1943-p-4.png
├── output/                      # Processed output files
│   ├── *_map.png               # Cropped map regions
│   ├── *_legend.png            # Extracted legends
│   ├── *_swatch_*.png          # Individual pattern swatches
│   ├── legend_extractions.json # Full extraction data
│   └── legend_swatches.json    # Pattern swatch metadata
├── legendoverlaymap.ipynb      # Zone overlay creation
├── legendpatternextract.ipynb  # Legend/pattern extraction
├── mapextract.ipynb            # Map region extraction
├── shapeextract.ipynb          # Shape segmentation
├── sam2.1_hiera_b+.yaml        # SAM2 config
├── sam2.1_hiera_base_plus.pt   # SAM2 model weights
├── requirements.txt            # Dependencies
└── README.md                   # This file
```

## Data Organization

### Input Folder (`input/`)
Contains raw newspaper page images:
- Place scanned newspaper pages here as PNG files
- Images should contain zoning/planning maps
- No subdirectories needed

### Output Folder (`output/`)
Auto-populated with processed results:
- `*_map.png` - Cropped map regions extracted from newspapers
- `*_legend.png` - Legend regions extracted from maps
- `*_swatch_*.png` - Individual pattern swatches from legends
- JSON files with metadata and extracted information

### Jupyter Notebooks

#### `mapextract.ipynb`
Extracts the map region from historical newspaper images and performs basic map analysis.
- Scans all PNG images in `input/` folder
- Loads SAM2 (Segment Anything Model 2) for image segmentation
- Detects and isolates the map area using edge detection and contour analysis
- Preprocesses map images for further analysis
- **Outputs:** Cropped map images saved to `output/*_map.png`

#### `shapeextract.ipynb`
Extracts geographic shapes and regions from the cropped map images.
- Uses SAM2 model for semantic segmentation
- Identifies distinct zones/regions based on visual boundaries
- Extracts shape polygons and their properties
- Useful for understanding map structure and zone boundaries

#### `legendpatternextract.ipynb`
Extracts legend information and pattern swatches from map legends using Gemini 2.0 Flash-Lite AI.
- Scans `output/*_map.png` files for legend extraction
- Detects legend location on the map image
- Extracts individual pattern swatches (colored/patterned symbols)
- Uses structured JSON output from Gemini to identify:
  - Zone types and their meanings
  - Pattern types (hatching, cross-hatch, dots, solid fills, etc.)
  - Visual descriptions of each symbol
- **Outputs:** 
  - Extracted swatches: `output/*_swatch_*.png`
  - Metadata: `output/legend_extractions.json` and `output/legend_swatches.json`

#### `legendoverlaymap.ipynb`
Matches extracted legend patterns to zones on the map to create a zone-annotated overlay.
- Loads extracted pattern swatches from legend
- Compares patterns to zones on the map using image matching/template matching
- Creates labeled overlay showing zone types across the map
- Outputs: Zone-labeled map overlays for analysis

### Configuration Files

#### `sam2.1_hiera_b+.yaml`
SAM2 model configuration file specifying:
- Model architecture (Hiera B+ backbone)
- Layer configurations
- Input/output specifications
- Used by `mapextract.ipynb` and `shapeextract.ipynb`

#### `sam2.1_hiera_base_plus.pt`
Pre-trained SAM2 model weights (~700MB+)
- Segment Anything Model 2 in Hiera Base Plus variant
- Used for semantic segmentation tasks across notebooks
- Downloaded from Meta's SAM2 releases

#### `requirements.txt`
Python package dependencies for the project
- Core: `torch`, `opencv-python`, `pillow`, `numpy`, `matplotlib`
- AI: `google-generativeai` (Gemini API), `sam2` (Segment Anything)
- Install with: `pip install -r requirements.txt`

### Data Files

#### Input Folder (`input/`)
Contains raw newspaper page images (already moved here):
- `abilene-reporter-news-apr-29-1946-p-19.png` - Historical newspaper page with zoning map
- `appleton-post-crescent-oct-24-1922-p-13.png` - Historical newspaper page with zoning map
- `atchison-daily-globe-nov-30-1943-p-4.png` - Historical newspaper page with zoning map

Add new images to this folder to process them.

#### `output/` Directory
Auto-populated with processed results:
- `*_map.png` - Cropped map images extracted from newspaper pages
- `*_legend.png` - Extracted legend regions from maps
- `*_swatch_*.png` - Individual pattern swatches from legends
- `legend_extractions.json` - Full structured legend extraction data (pattern types, meanings, etc.)
- `legend_swatches.json` - Metadata for each pattern swatch (meaning, paths, bounding boxes)

## Workflow

1. **Start with `mapextract.ipynb`**
   - Load raw newspaper images
   - Extract map regions
   - Generate cropped map PNGs

2. **Run `legendpatternextract.ipynb`**
   - Extract legends from cropped maps
   - Identify and save pattern swatches
   - Generate legend metadata JSON files

3. **Run `shapeextract.ipynb`**
   - Segment zones using SAM2
   - Extract shape boundaries

4. **Run `legendoverlaymap.ipynb`**
   - Match legend patterns to map zones
   - Create annotated zone overlays
   - Generate final labeled maps

## Setup

```bash
# Create and activate virtual environment
python -m venv .venv
source .venv/bin/activate  # On macOS/Linux
# or
.venv\Scripts\activate  # On Windows

# Install dependencies
pip install -r requirements.txt

# Set up Gemini API key (for legendpatternextract.ipynb)
export GOOGLE_API_KEY="your-api-key-here"
```

## Output Structure

```
output/
├── abilene-reporter-news-apr-29-1946-p-19_map.png
├── abilene-reporter-news-apr-29-1946-p-19_legend.png
├── abilene-reporter-news-apr-29-1946-p-19_swatch_0_*.png
├── abilene-reporter-news-apr-29-1946-p-19_swatch_1_*.png
├── appleton-post-crescent-oct-24-1922-p-13_map.png
├── appleton-post-crescent-oct-24-1922-p-13_legend.png
├── appleton-post-crescent-oct-24-1922-p-13_swatch_0_*.png
├── atchison-daily-globe-nov-30-1943-p-4_map.png
├── atchison-daily-globe-nov-30-1943-p-4_legend.png
├── atchison-daily-globe-nov-30-1943-p-4_swatch_0_*.png
├── legend_extractions.json      # Complete legend extraction data
└── legend_swatches.json         # Pattern swatch metadata
```

## Requirements

- Python 3.11+
- CUDA/MPS (GPU recommended for faster SAM2 inference)
- Gemini API key (for pattern extraction)
- ~2GB disk space for models and outputs
