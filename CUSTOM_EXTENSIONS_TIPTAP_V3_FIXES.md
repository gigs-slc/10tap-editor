# Custom Extensions TipTap v3 Migration Fixes

This document provides specific fixes needed for each custom TipTap extension to be compatible with TipTap v3.

## Table of Contents
1. [Import Changes Required](#import-changes-required)
2. [Extension-by-Extension Fixes](#extension-by-extension-fixes)
3. [Main Configuration Files](#main-configuration-files)

---

## Import Changes Required

### Critical Package Changes in TipTap v3

| Old Import (v2) | New Import (v3) |
|-----------------|-----------------|
| `@tiptap/extensions` | Individual packages (see below) |
| `@tiptap/extension-list` | Individual list packages |
| `UndoRedo` | `History` |
| `prosemirror-*` | Use `@tiptap/pm/*` for consistency |

### Individual Extension Imports

```typescript
// ❌ OLD - TipTap v2
import { Focus, UndoRedo, Dropcursor, Gapcursor, CharacterCount } from '@tiptap/extensions'
import { BulletList, OrderedList, ListItem, ListKeymap, TaskList, TaskItem } from '@tiptap/extension-list'
import { Placeholder } from "@tiptap/extensions"

// ✅ NEW - TipTap v3
import { Focus } from '@tiptap/extension-focus'
import { History } from '@tiptap/extension-history' // renamed from UndoRedo
import { Dropcursor } from '@tiptap/extension-dropcursor'
import { Gapcursor } from '@tiptap/extension-gapcursor'
import { CharacterCount } from '@tiptap/extension-character-count'
import { BulletList } from '@tiptap/extension-bullet-list'
import { OrderedList } from '@tiptap/extension-ordered-list'
import { ListItem } from '@tiptap/extension-list-item'
import { ListKeymap } from '@tiptap/extension-list-keymap'
import { TaskList } from '@tiptap/extension-task-list'
import { TaskItem } from '@tiptap/extension-task-item'
import { Placeholder } from '@tiptap/extension-placeholder'
```

---

## Extension-by-Extension Fixes

### 1. **customPlaceholder.ts**

**Location**: `tiptap-extensions/CustomExtensions/customPlaceholder.ts`

**Current Issue**: Importing from bundled `@tiptap/extensions`

**Fix Required**:
```typescript
// Line 4 - CHANGE FROM:
import {Placeholder} from "@tiptap/extensions";

// TO:
import { Placeholder } from "@tiptap/extension-placeholder";
```

**No other changes needed** - The extension logic remains compatible.

---

### 2. **ActionButton.ts**

**Location**: `tiptap-extensions/CustomExtensions/ActionButton.ts`

**Status**: ✅ **No changes needed**
- Already imports correctly from `@tiptap/core`
- Node creation API remains the same in v3

---

### 3. **CleanPasteExtension.ts**

**Location**: `tiptap-extensions/CustomExtensions/CleanPasteExtension.ts`

**Current imports**:
```typescript
import { Extension } from '@tiptap/core'
import { Plugin } from 'prosemirror-state'
```

**Recommended fix** (for consistency):
```typescript
// CHANGE FROM:
import { Plugin } from 'prosemirror-state'

// TO:
import { Plugin } from '@tiptap/pm/state'
```

---

### 4. **exitListOnBackspace.ts**

**Location**: `tiptap-extensions/CustomExtensions/exitListOnBackspace.ts`

**Current imports**:
```typescript
import { Extension } from '@tiptap/core'
import { Selection } from 'prosemirror-state'
```

**Recommended fix**:
```typescript
// CHANGE FROM:
import { Selection } from 'prosemirror-state'

// TO:
import { Selection } from '@tiptap/pm/state'
```

---

### 5. **Comments Extension**

**Location**: `tiptap-extensions/CustomExtensions/comments/`

#### Files to update:
- **CommentExtension.ts**: Check for any ProseMirror imports
- **commentDataHelpers.ts**: Already using `@tiptap/core` ✅

**Fixes**: Update any direct ProseMirror imports to use `@tiptap/pm/*`

---

### 6. **Slash Commands Extension**

**Location**: `tiptap-extensions/CustomExtensions/slash/`

#### Files to check:
- **extension.ts**: Verify Extension import is from `@tiptap/core`
- **commands.ts**: Check for any extension imports

**Status**: Likely compatible, but verify no bundled imports are used.

---

### 7. **Suggestions Extension**

**Location**: `tiptap-extensions/CustomExtensions/suggestions/`

This is the most complex extension with multiple ProseMirror integrations.

#### Critical files to update:
- **All files in `prosemirror-suggestion-mode/`**: Update ProseMirror imports

**Recommended changes**:
```typescript
// For all ProseMirror imports, change from:
import { Something } from 'prosemirror-state'
import { Something } from 'prosemirror-view'
import { Something } from 'prosemirror-model'
import { Something } from 'prosemirror-transform'

// To:
import { Something } from '@tiptap/pm/state'
import { Something } from '@tiptap/pm/view'
import { Something } from '@tiptap/pm/model'
import { Something } from '@tiptap/pm/transform'
```

---

### 8. **lineHeight.ts and fontSize.ts**

**Location**: `tiptap-extensions/CustomExtensions/`

**Status**: ✅ **Likely compatible**
- Already import from `@tiptap/core`
- Extension API unchanged in v3

---

## Main Configuration Files

### **getV1Extensions and getV2Extensions**

These files contain the main extension configuration and have the most critical changes:

**Files to update**:
- Files that export `getV1Extensions()`
- Files that export `getV2Extensions()`

**Required changes**:

```typescript
// 1. UPDATE IMPORTS - Replace these lines:
import { BulletList, OrderedList, ListItem, ListKeymap, TaskList, TaskItem } from '@tiptap/extension-list'
import { Focus, UndoRedo, Dropcursor, Gapcursor, CharacterCount } from '@tiptap/extensions'

// WITH:
import { BulletList } from '@tiptap/extension-bullet-list'
import { OrderedList } from '@tiptap/extension-ordered-list'
import { ListItem } from '@tiptap/extension-list-item'
import { ListKeymap } from '@tiptap/extension-list-keymap'
import { TaskList } from '@tiptap/extension-task-list'
import { TaskItem } from '@tiptap/extension-task-item'
import { Focus } from '@tiptap/extension-focus'
import { History } from '@tiptap/extension-history' // renamed from UndoRedo
import { Dropcursor } from '@tiptap/extension-dropcursor'
import { Gapcursor } from '@tiptap/extension-gapcursor'
import { CharacterCount } from '@tiptap/extension-character-count'

// 2. UPDATE EXTENSION ARRAY - In the extensions array, replace:
UndoRedo,

// WITH:
History, // or History.configure({...}) if you have custom config
```

---

## Quick Migration Script

Run this in your project root to automatically fix most imports:

```bash
#!/bin/bash

# Fix Placeholder imports
find tiptap-extensions -type f \( -name "*.ts" -o -name "*.tsx" \) -exec sed -i '' \
  's|from "@tiptap/extensions"|from "@tiptap/extension-placeholder"|g' {} \;

# Fix ProseMirror imports to use @tiptap/pm
find tiptap-extensions -type f \( -name "*.ts" -o -name "*.tsx" \) -exec sed -i '' \
  -e 's|from "prosemirror-state"|from "@tiptap/pm/state"|g' \
  -e 's|from "prosemirror-view"|from "@tiptap/pm/view"|g' \
  -e 's|from "prosemirror-model"|from "@tiptap/pm/model"|g' \
  -e 's|from "prosemirror-transform"|from "@tiptap/pm/transform"|g' \
  -e 's|from '\''prosemirror-state'\''|from '\''@tiptap/pm/state'\''|g' \
  -e 's|from '\''prosemirror-view'\''|from '\''@tiptap/pm/view'\''|g' \
  -e 's|from '\''prosemirror-model'\''|from '\''@tiptap/pm/model'\''|g' \
  -e 's|from '\''prosemirror-transform'\''|from '\''@tiptap/pm/transform'\''|g' {} \;

# Note: List and other extension imports need manual fixing due to complexity
```

---

## Testing Checklist

After making these changes:

1. ✅ Verify all imports resolve correctly
2. ✅ Check TypeScript compilation (`tsc --noEmit`)
3. ✅ Test each extension functionality:
   - [ ] Placeholder appears correctly
   - [ ] Action buttons work
   - [ ] Clean paste functions
   - [ ] Exit list on backspace works
   - [ ] Comments system functions
   - [ ] Slash commands menu appears
   - [ ] Suggestions/track changes work
   - [ ] Font size and line height adjustments work
4. ✅ Ensure document schema remains compatible
5. ✅ Test undo/redo functionality (now History)

---

## Summary of Changes

| Extension | Changes Needed | Priority | Complexity |
|-----------|---------------|----------|------------|
| customPlaceholder.ts | Import fix | HIGH | Low |
| Main config files | Multiple import fixes | HIGH | Medium |
| Suggestions extension | ProseMirror imports | MEDIUM | High |
| CleanPasteExtension | ProseMirror import | LOW | Low |
| exitListOnBackspace | ProseMirror import | LOW | Low |
| ActionButton | None | - | - |
| Comments | Check imports | MEDIUM | Medium |
| Slash commands | Check imports | MEDIUM | Low |

## Notes

- **Most critical fixes**: The main configuration files with list imports and the customPlaceholder
- **Schema compatibility**: All these changes are import-related; the actual schema and functionality remain unchanged
- **ProseMirror imports**: While not strictly required, using `@tiptap/pm/*` ensures version consistency
- **History vs UndoRedo**: This is a naming change in v3; functionality is identical

---

## Next Steps

1. Apply the import fixes listed above
2. Run TypeScript compilation to catch any remaining issues
3. Test each extension in the editor
4. Update any custom extension documentation to reflect v3 patterns