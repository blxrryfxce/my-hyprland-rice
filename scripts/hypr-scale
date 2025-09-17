#!/bin/bash

# Detect first monitor automatically
MONITOR=$(hyprctl monitors | grep -m1 "Monitor" | awk '{print $2}')

# Get current scale
CURRENT_SCALE=$(hyprctl monitors | grep -A10 "$MONITOR" | grep "scale:" | awk '{print $2}')
CURRENT_SCALE=$(awk -v c="$CURRENT_SCALE" 'BEGIN{printf "%.2f", c}')  # normalize

# Step size
STEP=0.25

# Allowed Hyprland clean scales for +, capped at 1.5
CLEAN_SCALES=(1.0 1.25 1.5)

if [[ "$1" == "+" ]]; then
    # Find next clean scale greater than current, max 1.5
    NEXT_SCALE=$CURRENT_SCALE
    for s in "${CLEAN_SCALES[@]}"; do
        awk -v s="$s" -v c="$CURRENT_SCALE" 'BEGIN{if(s>c){exit 0}else{exit 1}}'
        if [ $? -eq 0 ]; then
            NEXT_SCALE=$s
            break
        fi
    done
elif [[ "$1" == "-" ]]; then
    # Decrease by STEP, may trigger warning
    NEXT_SCALE=$(awk -v s="$CURRENT_SCALE" -v step="$STEP" 'BEGIN{printf "%.2f", s-step}')
else
    echo "Usage: $0 [+|-]"
    exit 1
fi

# Clamp scale to 0.5 minimum
if awk "BEGIN {exit !($NEXT_SCALE < 0.5)}"; then NEXT_SCALE=0.50; fi

# Apply scale, suppress terminal output
hyprctl keyword monitor "$MONITOR,preferred,auto,$NEXT_SCALE" >/dev/null 2>&1
echo "Monitor $MONITOR scale set to $NEXT_SCALE"

