"""
Enhanced Python Function Generator - Quasi-Agent
===============================================
Creates a complete Python function with documentation and tests using 3-step prompting.
"""

from litellm import completion
from typing import List, Dict, Tuple
import re
import os

def generate_response(messages: List[Dict]) -> str:
    """Call LLM to get response"""
    response = completion(
        model="openai/gpt-4o-mini",
        messages=messages,
        max_tokens=1024
    )
    return response.choices[0].message.content

def extract_code_block(response: str) -> str:
    """
    Extract clean code block from LLM response.
    Handles various formats the LLM might use.
    """
    # First try to find markdown code blocks
    if '```' in response:
        # Split by ``` and get the content between first pair
        parts = response.split('```')
        if len(parts) >= 3:
            code_block = parts[1].strip()
            # Remove language identifier if present
            if code_block.startswith('python'):
                code_block = code_block[6:].strip()
            return code_block
    
    # Fallback: return the whole response if no code blocks found
    return response.strip()

def create_clean_assistant_response(code: str) -> str:
    """
    Create a clean assistant response containing only code.
    This helps maintain consistency in the conversation.
    """
    return f"```python\n{code}\n```"

def generate_filename(description: str) -> str:
    """Generate a clean filename from the function description"""
    # Clean the description to make it filename-safe
    filename = description.lower()
    filename = ''.join(c for c in filename if c.isalnum() or c.isspace())
    filename = filename.replace(' ', '_')[:40]  # Limit length
    
    # Remove trailing underscores and add extension
    filename = filename.strip('_') + '_complete.py'
    return filename

def save_function_to_file(code: str, description: str) -> str:
    """Save the final function code to a file"""
    filename = generate_filename(description)
    
    try:
        with open(filename, 'w') as f:
            f.write(code)
        return filename
    except Exception as e:
        print(f"Warning: Could not save to file - {e}")
        return None

def create_python_function(description: str, show_full_responses: bool = False) -> Tuple[str, str, str]:
    """
    Enhanced function generator with 3-step process.
    
    Args:
        description: What function the user wants to create
        show_full_responses: Whether to show full LLM responses or just extracted code
        
    Returns:
        Tuple of (basic_function, documented_function, final_code_with_tests)
    """
    
    print(f"🚀 Creating function: {description}")
    print("=" * 60)
    
    # Initialize conversation with system prompt
    messages = [
        {
            "role": "system", 
            "content": "You are an expert Python programmer. Write clean, efficient functions based on user descriptions. Always output code in ```python code blocks```."
        }
    ]
    
    # ==========================================================================
    # STEP 1: Generate Basic Function
    # ==========================================================================
    print("\n🔧 STEP 1: Generating Basic Function")
    print("Goal: Create a working function with core logic")
    
    messages.append({
        "role": "user",
        "content": f"Write a Python function that {description}. Output the function in a ```python code block```."
    })
    
    response1 = generate_response(messages)
    
    if show_full_responses:
        print("Full LLM Response:")
        print(response1)
        print()
    
    # Extract clean code
    basic_function = extract_code_block(response1)
    print("✅ Generated Basic Function:")
    print(basic_function)
    print()
    
    # Add clean assistant response for better context management
    messages.append({
        "role": "assistant", 
        "content": create_clean_assistant_response(basic_function)
    })
    
    # ==========================================================================
    # STEP 2: Add Documentation
    # ==========================================================================
    print("📚 STEP 2: Adding Documentation")
    print("Goal: Add comprehensive docstrings and parameter descriptions")
    
    messages.append({
        "role": "user",
        "content": "Add comprehensive documentation to this function including: function description, parameter descriptions with types, return value description, example usage, and edge cases. Output the complete documented function in a ```python code block```."
    })
    
    response2 = generate_response(messages)
    
    if show_full_responses:
        print("Full LLM Response:")
        print(response2)
        print()
    
    # Extract documented code
    documented_function = extract_code_block(response2)
    print("✅ Added Documentation:")
    print(documented_function)
    print()
    
    # Add clean assistant response
    messages.append({
        "role": "assistant", 
        "content": create_clean_assistant_response(documented_function)
    })
    
    # ==========================================================================
    # STEP 3: Add Test Cases
    # ==========================================================================
    print("🧪 STEP 3: Adding Test Cases")
    print("Goal: Add comprehensive unittest test cases")
    
    messages.append({
        "role": "user",
        "content": "Add comprehensive unittest test cases to this code. Include tests for: basic functionality, edge cases, error cases, and various input scenarios. Return the complete code with the function, imports, test class, and main block to run tests in a ```python code block```."
    })
    
    response3 = generate_response(messages)
    
    if show_full_responses:
        print("Full LLM Response:")
        print(response3)
        print()
    
    # Extract final code with tests
    final_code = extract_code_block(response3)
    print("✅ Added Test Cases:")
    print(final_code)
    print()
    
    # ==========================================================================
    # SAVE AND FINISH
    # ==========================================================================
    print("💾 SAVING RESULTS")
    filename = save_function_to_file(final_code, description)
    
    if filename:
        print(f"✅ Saved complete function to: {filename}")
    
    # Optionally show final conversation for debugging
    if show_full_responses:
        print("\n🔍 DEBUG: Final Conversation")
        print("=" * 30)
        for i, msg in enumerate(messages):
            print(f"{i+1}. {msg['role']}: {msg['content'][:100]}...")
        print()
    
    return basic_function, documented_function, final_code

def main():
    """Main interactive function"""
    print("🤖 Enhanced Python Function Generator")
    print("=" * 50)
    print("This tool creates a complete Python function with:")
    print("  • Basic functionality")
    print("  • Comprehensive documentation") 
    print("  • Unit tests")
    print("  • Auto-saved to file")
    print("=" * 50)
    
    # Get user input
    print("\nWhat kind of function would you like to create?")
    print("Example: 'calculates the factorial of a number'")
    print("Example: 'sorts a list of dictionaries by a specific key'")
    description = input("Your description: ").strip()
    
    if not description:
        print("❌ Please provide a function description!")
        return
    
    # Ask about verbosity
    show_full = input("\nShow full LLM responses? (y/n): ").lower().startswith('y')
    
    try:
        print("\n" + "="*60)
        print("🚀 STARTING GENERATION PROCESS")
        print("="*60)
        
        basic, documented, final = create_python_function(description, show_full)
        
        print("\n" + "="*60)
        print("🎉 SUCCESS! Function generation complete!")
        print("="*60)
        print("You now have:")
        print(f"  ✅ A working function")
        print(f"  ✅ Complete documentation")
        print(f"  ✅ Comprehensive tests")
        print(f"  ✅ Saved to file")
        
    except Exception as e:
        print(f"❌ Error occurred: {e}")
        print("Please check your API key and try again.")

# =============================================================================
# EXECUTION
# =============================================================================

if __name__ == "__main__":
    main()
