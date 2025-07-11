# Templify - Technical Implementation Guide for Cursor AI

## Core Implementation Workflow

### 1. Project Setup Commands
```bash
# Create Next.js project with TypeScript
npx create-next-app@latest templify --typescript --tailwind --app

# Navigate to project
cd templify

# Install core dependencies
npm install sharp multer tesseract.js colorthief vibrant.js

# Install UI dependencies
npm install lucide-react @radix-ui/react-dialog @radix-ui/react-progress

# Install development dependencies
npm install -D @types/multer @types/node
```

### 2. Environment Variables Setup
```env
# .env.local
GOOGLE_VISION_API_KEY=your_google_vision_key
CANVA_CLIENT_ID=your_canva_client_id
CANVA_CLIENT_SECRET=your_canva_client_secret
HUGGINGFACE_API_KEY=your_huggingface_key
NEXT_PUBLIC_BASE_URL=http://localhost:3000
```

### 3. Tailwind Configuration
```javascript
// tailwind.config.js
module.exports = {
  content: [
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: '#6366f1',
        secondary: '#f59e0b',
        accent: '#10b981',
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
      },
    },
  },
  plugins: [],
}
```

### 4. Core TypeScript Types
```typescript
// types/index.ts
export interface AnalysisResult {
  id: string;
  image: {
    url: string;
    width: number;
    height: number;
  };
  text: TextElement[];
  colors: ColorPalette;
  fonts: FontAnalysis[];
  layout: LayoutStructure;
  elements: DesignElement[];
}

export interface TextElement {
  text: string;
  position: { x: number; y: number; width: number; height: number };
  font: string;
  fontSize: number;
  color: string;
  confidence: number;
}

export interface ColorPalette {
  dominant: string;
  palette: string[];
  vibrant: string;
  muted: string;
}

export interface FontAnalysis {
  family: string;
  weight: string;
  style: string;
  usage: 'heading' | 'body' | 'caption';
  canvaEquivalent: string;
}

export interface CanvaTemplate {
  elements: CanvaElement[];
  background: string;
  dimensions: { width: number; height: number };
}
```

### 5. Image Upload API Implementation
```typescript
// pages/api/upload.ts
import { NextApiRequest, NextApiResponse } from 'next';
import multer from 'multer';
import sharp from 'sharp';
import { v4 as uuidv4 } from 'uuid';

const upload = multer({
  storage: multer.memoryStorage(),
  limits: { fileSize: 10 * 1024 * 1024 }, // 10MB
});

export default async function handler(req: NextApiRequest, res: NextApiResponse) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    // Process uploaded image
    const imageBuffer = req.body;
    const processedImage = await sharp(imageBuffer)
      .resize(2000, 2000, { fit: 'inside', withoutEnlargement: true })
      .jpeg({ quality: 90 })
      .toBuffer();

    const imageId = uuidv4();
    
    // Store temporarily (or upload to Vercel Blob)
    // Return image ID for processing
    res.status(200).json({ 
      imageId,
      message: 'Image uploaded successfully' 
    });
  } catch (error) {
    res.status(500).json({ error: 'Image processing failed' });
  }
}
```

### 6. Analysis Processing Logic
```typescript
// lib/imageAnalysis.ts
import Tesseract from 'tesseract.js';
import ColorThief from 'colorthief';
import { AnalysisResult } from '../types';

export async function analyzeImage(imageBuffer: Buffer): Promise<AnalysisResult> {
  const results: Partial<AnalysisResult> = {
    id: Date.now().toString(),
  };

  // Parallel processing for better performance
  const [textResults, colorResults, layoutResults] = await Promise.all([
    extractText(imageBuffer),
    extractColors(imageBuffer),
    analyzeLayout(imageBuffer),
  ]);

  return {
    ...results,
    text: textResults,
    colors: colorResults,
    layout: layoutResults,
  } as AnalysisResult;
}

async function extractText(imageBuffer: Buffer) {
  const { data: { text, words } } = await Tesseract.recognize(imageBuffer, 'eng');
  return processTextResults(words);
}

async function extractColors(imageBuffer: Buffer) {
  const colorThief = new ColorThief();
  const palette = await colorThief.getPalette(imageBuffer, 8);
  return processColorResults(palette);
}
```

### 7. Frontend Component Structure
```typescript
// components/ImageUpload.tsx
'use client';
import { useState, useCallback } from 'react';
import { Upload, Image as ImageIcon } from 'lucide-react';

export default function ImageUpload({ onUpload }: { onUpload: (file: File) => void }) {
  const [dragActive, setDragActive] = useState(false);

  const handleDrop = useCallback((e: React.DragEvent) => {
    e.preventDefault();
    setDragActive(false);
    
    const files = e.dataTransfer.files;
    if (files?.[0]) {
      onUpload(files[0]);
    }
  }, [onUpload]);

  return (
    <div
      className={`border-2 border-dashed rounded-lg p-8 text-center transition-colors ${
        dragActive ? 'border-primary bg-primary/10' : 'border-gray-300'
      }`}
      onDragOver={(e) => { e.preventDefault(); setDragActive(true); }}
      onDragLeave={() => setDragActive(false)}
      onDrop={handleDrop}
    >
      <ImageIcon className="mx-auto h-12 w-12 text-gray-400" />
      <p className="mt-2 text-sm text-gray-600">
        Drag & drop your design image here, or click to browse
      </p>
      <input
        type="file"
        accept="image/*"
        onChange={(e) => e.target.files?.[0] && onUpload(e.target.files[0])}
        className="hidden"
      />
    </div>
  );
}
```

### 8. Canva Integration
```typescript
// lib/templateBuilder.ts
import { AnalysisResult, CanvaTemplate } from '../types';

export async function createCanvaTemplate(analysis: AnalysisResult): Promise<string> {
  const template: CanvaTemplate = {
    elements: analysis.text.map(textElement => ({
      type: 'text',
      text: textElement.text,
      position: textElement.position,
      font: mapToCanvaFont(textElement.font),
      color: textElement.color,
    })),
    background: analysis.colors.dominant,
    dimensions: { width: analysis.image.width, height: analysis.image.height },
  };

  // Call Canva API to create template
  const response = await fetch('https://api.canva.com/v1/designs', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.CANVA_ACCESS_TOKEN}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(template),
  });

  const result = await response.json();
  return result.edit_url; // Return editable Canva URL
}

function mapToCanvaFont(detectedFont: string): string {
  const fontMapping: Record<string, string> = {
    'Arial': 'Arial',
    'Helvetica': 'Helvetica',
    'Times': 'Times New Roman',
    // Add more mappings
  };
  
  return fontMapping[detectedFont] || 'Arial';
}
```

### 9. Main Page Implementation
```typescript
// app/page.tsx
'use client';
import { useState } from 'react';
import ImageUpload from '../components/ImageUpload';
import AnalysisReport from '../components/AnalysisReport';
import { AnalysisResult } from '../types';

export default function Home() {
  const [isAnalyzing, setIsAnalyzing] = useState(false);
  const [analysisResult, setAnalysisResult] = useState<AnalysisResult | null>(null);

  const handleImageUpload = async (file: File) => {
    setIsAnalyzing(true);
    
    try {
      const formData = new FormData();
      formData.append('image', file);
      
      const response = await fetch('/api/analyze', {
        method: 'POST',
        body: formData,
      });
      
      const result = await response.json();
      setAnalysisResult(result);
    } catch (error) {
      console.error('Analysis failed:', error);
    } finally {
      setIsAnalyzing(false);
    }
  };

  return (
    <div className="min-h-screen bg-gray-50">
      <div className="max-w-4xl mx-auto p-6">
        <h1 className="text-3xl font-bold text-center mb-8">Templify</h1>
        
        {!analysisResult ? (
          <ImageUpload onUpload={handleImageUpload} />
        ) : (
          <AnalysisReport result={analysisResult} />
        )}
        
        {isAnalyzing && (
          <div className="text-center mt-8">
            <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-primary mx-auto"></div>
            <p className="mt-4 text-gray-600">Analyzing your design...</p>
          </div>
        )}
      </div>
    </div>
  );
}
```

## Cursor AI Prompts for Development

### Initial Setup Prompt
```
Create a Next.js 14 project called "templify" with TypeScript and Tailwind CSS. Set up the basic project structure with components for image upload, analysis results, and Canva integration. Include proper TypeScript types for image analysis results.
```

### Component Development Prompts
```
1. "Create a drag-and-drop image upload component with Tailwind styling"
2. "Build an analysis report component that displays text, colors, and fonts"
3. "Create a color palette component that shows extracted colors with hex codes"
4. "Build a font analysis component that lists detected fonts and their properties"
5. "Create a Canva export button that opens templates in a new tab"
```

### API Development Prompts
```
1. "Create an API endpoint for image upload with Sharp processing"
2. "Build an analysis API that uses Tesseract.js for OCR"
3. "Create a color extraction API using Color Thief"
4. "Build a Canva integration API for template creation"
```

## Development Workflow

### Phase 1: Basic Setup
1. Create Next.js project with TypeScript
2. Set up Tailwind CSS configuration
3. Create basic component structure
4. Implement image upload functionality

### Phase 2: Analysis Features
1. Integrate OCR with Tesseract.js
2. Add color extraction with Color Thief
3. Implement font detection logic
4. Create analysis results display

### Phase 3: Canva Integration
1. Set up Canva Connect API
2. Build template generation logic
3. Create export functionality
4. Test end-to-end workflow

### Phase 4: Polish & Deploy
1. Add loading states and error handling
2. Optimize performance and user experience
3. Deploy to Vercel
4. Test production environment

This guide provides everything Cursor AI needs to build Templify with the exact tech stack and implementation details.
