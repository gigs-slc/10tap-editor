# TipTap v3 Migration Guide for 10tap-editor

## Overview
This document outlines the migration from TipTap v2 to TipTap v3 for the 10tap-editor package, ensuring schema compatibility between web and mobile implementations.

## Key Changes

### 1. Dependencies Updated
All TipTap packages have been upgraded to v3.0.0:
- `@tiptap/core`: ^3.0.0
- `@tiptap/react`: ^3.0.0
- `@tiptap/pm`: ^3.0.0
- All extension packages: ^3.0.0
- Added `@tiptap/extension-text` (now required explicitly)

### 2. Import Changes

#### TextStyle Import
```typescript
// Before (v2)
import TextStyle from '@tiptap/extension-text-style';

// After (v3)
import { TextStyle } from '@tiptap/extension-text-style';
```

#### Content Type Import
```typescript
// Before (v2)
import type { Content } from '@tiptap/react';

// After (v3)
import type { Content } from '@tiptap/core';
```

#### Extension Import
```typescript
// Before (v2)
import { Extension } from '@tiptap/react';

// After (v3)
import { Extension } from '@tiptap/core';
```

### 3. Hook API Changes

#### useEditor Hook
The `useEditor` hook now returns an object instead of the editor directly:

```typescript
// Before (v2)
const editor = useEditor({
  content,
  onCreate: () => { /* ... */ },
  onUpdate: (onUpdate) => {
    onUpdate.editor.commands.focus();
  }
});

// After (v3)
const { editor } = useEditor({
  content,
  onCreate: ({ editor }) => { /* ... */ },
  onUpdate: ({ editor }) => {
    editor.commands.focus();
  }
});
```

### 4. Type Compatibility Fixes

For ProseMirror type conflicts between versions, type assertions were added:
```typescript
// In HighlightSelection.ts
return DecorationSet.create(newEditorState.doc as any, decorations);
```

## Schema Compatibility

### Preserved Node and Mark Specs
All ProseMirror node and mark specifications remain identical between TipTap v2 and v3, ensuring:
- Document structure compatibility
- Attribute preservation
- parseDOM/toDOM rules unchanged
- Content model consistency

### Supported Extensions
The following extensions are fully compatible with TipTap v3:
- Bold, Italic, Strike, Underline, Code
- Heading, Paragraph, BlockQuote
- BulletList, OrderedList, ListItem
- TaskList, TaskItem
- Link, Image
- Color, Highlight
- History (undo/redo)
- Placeholder
- DropCursor
- HardBreak

## Bridge System Updates

All bridge extensions have been updated to work with TipTap v3:
- Command APIs remain consistent
- State management unchanged
- Event handling preserved
- CSS injection compatible

## Testing Recommendations

1. **Document Compatibility**: Test existing documents created with TipTap v2 to ensure they render correctly
2. **Schema Validation**: Verify JSON schema matches between web and mobile
3. **Extension Functionality**: Test all toolbar actions and keyboard shortcuts
4. **Cross-Platform Sync**: Ensure documents created on mobile render correctly on web and vice versa

## Migration Steps for App Integration

1. Update your app's package.json to use the upgraded 10tap-editor
2. Rebuild the editor: `yarn editor:build`
3. Rebuild the library: `yarn prepare`
4. Test with existing documents
5. Update any custom extensions to use TipTap v3 APIs

## Breaking Changes

None identified that affect the public API of 10tap-editor. All changes are internal implementation details.

## Performance Improvements

TipTap v3 includes:
- Better tree-shaking support
- Improved TypeScript types
- Optimized extension loading
- Enhanced memory management

## Support

For issues related to this migration, please refer to:
- [TipTap v3 Documentation](https://tiptap.dev/docs)
- [TipTap Migration Guide](https://tiptap.dev/docs/resources/whats-new)
- [ProseMirror Documentation](https://prosemirror.net/docs/guide/)